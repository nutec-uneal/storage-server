# Storage Server

O servidor de armazenamento permite que você centralize seus armazentos em somente uma máquina, utilizando o conceito de NAS (Network Attached Storage), com o software TrueNAS.

## Sumário

- [Storage Server](#storage-server)
  - [Sumário](#sumário)
  - [Requisitos](#requisitos)
  - [Instalação](#instalação)
  - [Configuração](#configuração)
    - [Discos](#discos)
    - [Usuários](#usuários)
    - [Rede](#rede)
    - [Interface Web em Https](#interface-web-em-https)
    - [Agendamento de tarefas](#agendamento-de-tarefas)
    - [SSD para cache](#ssd-para-cache)


<br>

## Requisitos

Para montar um servidor TrueNAS precisamos ter os seguintes itens:

- Processador Dual-Core 64-bit ou superior;
- Uma máquina com suporte a DDR3 ou superior;
- Mínimo 8Gb de ram, 16Gb recomendado;
- Um gabinete com várias baias para HDs;
- Um Ssd para o sistema e outro para cache*;
- No mínimo 2 HDs com o mesmo tamanho de espaço*;
- Placa de Rede Gigabit;
- Uma boa fonte que comporte tudo.

***Obs: A quantidade de HDs pode variar dependendo da sua necessidade e qual o modo de RAID (Redundant Array of Independent Disks) escolhido, porém sempre lembre que todos devem possuir o mesmo tamanho. A quantidade de ssd para cache pode variar de acordo com sua necessidade.***

Site oficial TrueNAS: (https://www.truenas.com/#)

Para qualquer dúvida consulte a documentação do TrueNAS: (https://www.truenas.com/docs/)

Versão utilizada no teste: TrueNAS-SCALE-22.12.1

<br>

## Instalação

Para realizarmos a instalação do TrueNAS precisamos fazer o download da sua Iso no site oficial. Escolha a versão Scale que é baseada no Debian, a versão Core é baseada em FreeBSD. 

Link download versão scale: (https://www.truenas.com/download-truenas-scale/#) 

Após o download queime em um pendrive e dê inicio a instalação.

1. A primeira tela vai aparecer 4 opções, selecione Install/Upgrade.
2. Após ele vai perguntar em qual disco o sistema deve ser instalado, selecione o Ssd.
3. Agora ele vai dar um aviso que vai apagar tudo do disco selecionado, cheque se o disco é o correto e pode prosseguir.
4. Agora é vai perguntar qual usuário será utilizado para acessar a interface web, selecione Admin.
5. Informe uma senha para o usuário Admin.
6. Após isso o sistema vai ser instalado, aguarde o processo acabar.
7. Quando encerrar vai abrir o terminal com várias opções e com o ip da máquina para acessar a interface web.
8. Digite o ip no navegador e acesse usando o usuário escolhido e a senha criada.

<br>

## Configuração

### Discos

Após acessar a interface web você precisa configurar os hds em algum tipo de RAID, se você estiver armazenando arquivos importantes recomendo usar algum tipo com redudância. Para isso:

1. Acesse a área de ***Storage*** no menu lateral.
2. Clique em Create Pool.
3. Você precisa colocar um nome. 
4. Abaixo ele vai exibir quais são os discos disponíveis para usar, selecione os que você vai utilizar.
5. No fim da janela ele vai exibir os discos selecionados e qual o tipo de RAID será usado, você pode alterar clicando em cima na opção.
6. Verifique se tudo esta correto e clique em Create.

Pronto agora você tem uma Pool e pode compartilhar ela atráves da rede. Existe vários protocolos de compartilhamento, selecione o que mais atende as suas necessidades, nesse exemplo será escolhido o Samba (SMB). Para isso:

1. Acese a área de ***Shares*** no menu lateral.
2. Será exibido os protocolos disponíveis.
3. Na área SMB clique em Add e será aberta uma nova janela.
4. Informe o caminho da Pool criada anteriormente.
5. Informe um nome para seu compartilhamento.
6. Informe o propósito do compartilhamento, ele varia dependendo da necessidade, nesse caso será escolhido: Private SMB Datasets and Shares.
7. Clique em Enabled para habilitar o compartilhamento.
8. Verifique tudo e clique em Save.

Agora você tem um serviço samba rodando no seu servidor, para testar utilize uma máquina cliente:

- Em Windows -> Abra o Explorer, clique na barra de caminho lá em cima e digite: \\\IP-do-servidor.

- Em Linux -> Acesse seu gerenciador de arquivos, clique na barra de caminho lá em cima e digite: smb://IP-do-servidor.

Mas nesse caso será pedido um usuário e senha, porém o que utilizamos para acessar a interface web não vai funcionar, assim precisamos criar um novo usuário.

### Usuários

Você pode criar vários usuários e limitar o que cada um pode fazer, tendo assim uma segurança. Nesse exemplo precisamos criar um usuário que possa acessar nossa pasta compartilhada via Samba. Para isso:

1. Acesse a área ***Credentials*** no menu lateral.
2. Será exibida várias opções, clique em Local Users.
3. Agora será exibido os usuários já existentes, para criar um novo clique em Add e será aberta um janela.
4. Informe o nome do responsável pelo usuário.
5. Informe um username.
6. Crie uma senha.
7. Abaixo na janela você pode adicionar ele em outros grupos auxiliares, nesse caso coloque ele em nogroup.
8. Na parte de diretórios pode deixar como esta.
9. Na parte de Autenticação, o shell desse usuário deve ser nologin.
10. As próximas caixas deixe desmarcada, marque somente Samba Authentication.
11. Verifique tudo e clique em Save.

Em uma máquina cliente tente acessar a pasta compartilhada e use o usuário criado e sua senha.

### Rede

No momento da instalação a rede do servidor é configurada usando DHCP, mas não queremos que o IP do servidor mude, assim precisamos configurar de forma estática. Para isso:

1. Acesse a área ***Network*** no menu lateral.
2. Para definir o IP clique em na sua placa de rede, exemplo.: eno1
3. Na janela que apareceu desmarque a opção DHCP.
4. Mais abaixo procure por Aliases e clique em Add.
5. Informe o IP que você deseja e sua máscara, exemplo.: 192.168.1.10/24
6. Verifique tudo e clique em salvar.
7. Vai aparecer um aviso perguntando se você quer testar as alterações ou reverter, se estiver do modo desejado clique em testar.
8. Você deverá ser derrubado da interface web pois estava acessando ela pelo IP antigo, abra outra aba no navegador e tente acessar com o novo IP.
9. Ainda na área Network, vá na janela **Global Configuration** e clique em Settings.
10. Caso queira você pode alter o Hostname da máquina e seu domínio.
11. Mais Abaixo na área de DNS Servers, informe no mínimo dois dns.
12. Em Default Gateway informe o gateway da sua rede, exemplo.: 192.168.1.1
13. Verifique tudo e clique em Save.

Pronto, agora o seu servidor tem um IP estático e acesso a internet.

***Obs: Caso você tenha algum problema com a configuração da rede, você pode refazer o processo acessando o terminal do próprio servidor e utilizando as opções disponíveis.***

### Interface Web em Https

Por padrão o TrueNAS vem habilitado para responder nas portas 80 e 443, porém conseguimos que tudo que bata na porta 80 seja redirecionada para a 443, deixando assim o acesso mais seguro. Para isso:

1. Clique em ***System Settings*** no menu lateral e selecione General.
2. Procure a janela **GUI** e clique em Settings.
3. Você precisa selecionar um tema.
4. Você precisa selecionar um certificado SSl, por padrão o TrueNAS já cria um porém não tem nenhum entidade autenticadora.
5. Você precisar informar qual interface será atentida, por padrão já vem 0.0.0.0, que significa qualquer coisa.
6. Caso você queira pode alter o valor das portas para http e htpps.
7. Um pouco abaixo selecione a caixa que força o redirecionamento de http para https.
8. Verifique tudo e clique em salvar.
9. Você pode ser derrubado da página caso esteja acessando via porta 80, para isso pasta abrir outra aba e acessar novamente.


***Obs: Para saber mais sobre certificados SSL/TLS consulte o  [Guia de Certificados](https://github.com/nutecuneal/proxy-manager-deployer#certificado-ssltls-https). A localização do certificado gerado pelo TrueNAS fica em Credentials/Certficates.***

### Agendamento de tarefas

O TrueNAS permite que você crie um agendamento de tarefas, seja para tarefas referentes ao sistema ou para verificação de saúde dos discos. Nesse exemplo vamos configurar um **Cron Job** para fazer reboot do servidor em uma determinada quantidade de dias. Para isso:

1. Acesse a área ***System Settings*** no menu lateral e selecione Advanced.
2. Procure pela janela **Cron Jobs** e clique em Add.
3. Vai aparecer um aviso, leia e continue.
4. Na nova janela, informe uma descrição caso deseje sobre o que esse tarefa vai fazer.
5. Você precisa informar qual comando será realizado, nesse exemplo é o comando reboot.
6. Você precisa informar qual usuário vai executar esse comando, nesse exemplo será o usuário root.
7. Você precisa decidir o agendamento de quando essa tarefa será executada, nesse exemplo será toda semana nos domingos.
8. Marque a caixa de Enabled.
9. Verifique tudo e clique em salvar.
10. De volta a janela do Cron Jobs você pode testar o comando clicando no ícone de **Play Jobs**.

Se tudo estiver funcionando a tarefa esta criada, você pode criar outras dependendo da sua necessidade.

Você pode fazer agendamento de verificação dos discos também, um exemplo seria checar a saúde dos discos. Para isso:

1. Acesse a área ***Data Protection*** no menu lateral.
2. Procure pela janela **S.M.A.R.T. Tests** e clique em Add.
3. Na nova janela você pode marcar se quer executar essa tarefa em todos os discos ou somente em um.
4. Você precisa decidir o tipo da verificação, nesse exemplo vamos escolher a verificação rápida.
5. Você pode adicionar uma descrição caso deseje sobre o que essa tarefa faz.
6. Você precisa decidir o agendamento de quando essa tarefa será executada, nesse exemplo será todo dia as 23:59hrs.
7. Verifique tudo e clique em salvar.
8. Aguarde a execução da tarefa e veja se ocorreu como planejado.

Você pode criar outras tarefas dependendo da sua necessidade.

### SSD para cache

O TrueNAS já utiliza a memória ram disponível para fazer cache, mas isso não pode ser suficiente, e para adicionar mais memória o valor pode ficar muito alto. Porém podemos utilizar um SSD (Solid State Drive) ou mais para fazer o cache dos dados mais acessados, agilizando o acesso a essas informações e custando um pouco menos do que memórias.

Temos duas formas de adicionar um SSD para cache, a primeira é adicionar ele na hora da criação da **Pool** e a segunda é quando a **Pool** já foi criada usando somente os HDs.

Primeira opção:

1. Para isso faça o processo descrito na criação de uma pool em [Discos](#discos);
2. Quando estiver adicionando o nome da Pool, você deve procurar uma caixa de seleção escrito Add Vdev.
3. Clique em Add Vdev e vai aparecer várias opções, nesse caso precisamos apenas da opção cache.
4. Abaixo vai aparecer os discos disponíveis, selecione os HDs e o SSD.
5. No fim coloque os HDs para fazer o seu RAID e coloque o SDD na área de Vdev para ficar responsável pelo cache.
6. Verifique tudo e clique em Create.

Segunda opção:

1. Acesse a área ***Storage*** no menu lateral.
2. Procure pela janela **Topology** e clique em Manage Devices.
3. Agora procure a opção **Add VDEV** e clique nela.
4. Na nova janela, clique em Add Vdev e vai aparecer várias opções, nesse caso precisamos apenas da opção cache.
5. Abaixo vai aperecer os discos disponíveis, selecione o SSD.
6. Adicione o SSD no Vdev cache para ser o responsável pelo cache.
7. Verifique tudo e clique em Add Vdevs.

Utilizando qualquer das opções acima agora temos um disco somente para cache, isso vai economizar nosso tempo e permite uma escalabilidade no servidor. Para adicionar mais SSDs só repetir a segunda opção.