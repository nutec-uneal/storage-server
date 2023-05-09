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
    - [Discos com problemas](#discos-com-problemas)
    - [Backup](#backup)
    - [Alterar idioma e horário](#alterar-idioma-e-horário)
  - [Autenticação em 2 fatores (2FA)](#autenticação-em-2-fatores-2fa)
  - [Atualização](#atualização)


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

***Site oficial TrueNAS: (https://www.truenas.com/#)***

***Para qualquer dúvida consulte a documentação do TrueNAS: (https://www.truenas.com/docs/)***

***Versão utilizada no teste: TrueNAS-SCALE-22.12.1***

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

Utilizando qualquer uma das opções acima agora temos um disco somente para cache, isso vai economizar nosso tempo e permite uma escalabilidade no servidor. Para adicionar mais SSDs só repetir a segunda opção.

### Discos com problemas

Caso algum disco apresente problema você pode fazer a sua substituição, caso esteja utilizando algum RAID com redundância, nem dado será perdido. Nesse exemplo estamos utilizando o RAID 1, para fazer a troca:

Caso o novo disco já esteja instalado no servidor
    
  1. Acesse a área ***Storage*** no menu lateral.
  2. Procure pela janela **Topology** e clique em **Manage Devices**.
  3. Clique em **MIRROR** e vai exibir os discos utilizados nesse RAID.
  4. Clique sobre o disco que esta com defeito.
  5. Ao lado vai aparecer várias informações sobre o disco, faça uma rolagem até encontrar **Disk Info**.
  6. Clique no botão Replace.
  7. Vai perguntar qual outro disco será colocado no lugar.
  8. Você também tem a opção de forçar a troca, use-a caso necessário.
  9. Verifique se selecionou o disco certo e clique em **Replace Disk**.
  10. Aguarde o processo acabar.
  11. Verifique se nenhum dado foi perdido durante a mudança.
  12. Desligue o servidor, tire o disco defeituoso e ligue o servidor novamente.

Com o disco fora do servidor

  1. Desligue o servidor.
  2. Retire o servidor defeituoso e coloque o novo no lugar.
  3. Ligue o servidor novamente e acesse a interface.
  4. Para adicionar o novo disco na pool refaça os passos de 1-11, descritos acima no exemplo do disco já instalado no sistema.
  5. A diferença será no passo 4, pois ao invés de aparecer o nome do disco vai aparecer uma numeração.
  6. Verifique se nenhum dado foi perdido.

***Obs: Lembre que os discos devem ser do mesmo tamanho na hora da substituição por um novo. Verifique também os serviços que você esta utilizando, pois eles podem parar devido a troca dos discos, exemplo.: Samba***

### Backup

Precisamos ter backup dos nossos dados e arquivos de configuração caso o servidor apresente algum problema, temos como fazer backup utilizando Snapshots, fazendo backup em Cloud e backup das configurações do sistema. Vamos utilizar as opções de configurações do sistema e snapshots nesse exemplo:

Arquivo de Configuração

  1. Acesse a área ***System Settings*** no menu lateral e clique em General.
  2. Procure por uma caixa de seleção no canto superior direito onde esta escrito **Manage Configuration**.
  3. Clique na caixa e selecione a opção **Download File**.
  4. Vai aparecer um aviso, leia com bastante atenção.
  5. A opção **Export Password Secret Seed** deve ser marcada caso queira utilizar essas configurações em uma instalação em outra máquina, mas atenção pois isso vai descriptografar todas as senhas.
  6. Para usar na própria máquina não precisa marcar.
  7. Salve o arquivo em um lugar seguro, onde ninguém desconhecido possa acessar. Recomendado colocar em um local onde seja feito backup regularmente.
  8. Para enviar o arquivo para o sistema, refaça os passos 1 e 2.
  9. Clique na caixa e selecione **Upload File**, vai aparecer um aviso, leia com atenção.
  10. Clique para selecionar o arquivo e após clique em Upload.
  11. O servidor vai reiniciar para aplicar as configurações.
  12. Acesse o servidor e verifique se tudo foi aplicado corretamente.

Snapshot Manual

  1. Acesse a área ***Datasets*** no menu lateral.
  2. Clique sobre a pasta onde você vai querer tirar o snapshot.
  3. Ao lado vai aparecer várias janelas flutuantes, faça a rolagem até encontrar **Data Protection**.
  4. Clique em **Manage Snapshots** e vai abrir uma nova janela.
  5. Clique em Add.
  6. Você precisa escolher o caminho da pasta para fazer o snapshot, nesse exemplo será: **test/arquivos**
  7. Você pode definir um novo nome para o snapshot ou deixar o que já foi criado automáticamente.
  8. Você pode marcar a opção **Recursive** para adicionar as sub-pastas no snapshot também, nesse exemplo iremos selecionar.
  9. Verifique tudo e clique em **Save**.

Snapshot Automático

   1. Acesse a área **Data Protection** no menu lateral.
   2. Procure por **Periodic Snapshot Tasks** e clique em Add.
   3. Você precisa escolher o caminho da pasta para fazer o snapshot, nesse exemplo será: **test/arquivos**
   4. Você precisa definir um tempo de vida para o snapshot, isso varia de acordo com sua necessidade, nesse exemplo vamos escolher: **1 Week**.
   5. Em **Naming Schema** você pode alterar a forma de como o snapshot será nomeado, nesse exemplo a forma automática já atende as necessidades.
   6. Em **Exclude** você pode informar quais pastas não estaram no snapshot.
   7. Você pode marcar a opção **Recursive** para adicionar as sub-pastas no snapshot também, nesse exemplo iremos selecionar.
   8. Você precisa definir um agendamento para essa tarefa, nesse exemplo será: **Toda quarta-feira às 23:00 horas**.
   9. Desmarque a opção **Allow Taking Empty Snapshots**, pois ela cria snapshots vazios, e nos só queremos fazer um snapshot quando houver alterações nos dados.
   10. Verifique tudo e clique em Save.

Recuperar o Snapshot

   1. Acesse a área **Data Protection** no menu lateral.
   2. Procure por **Periodic Snapshot Tasks** e clique em Snapshots.
   3. Na nova janela procure pelo snapshot que você quer recuperar e clique nele.
   4. Ao clicar vai aparecer três opções, selecione Rollback.
   5. Vai aparecer uma nova janela, perguntando em qual situação o rollback deve para, isso varia devido a sua necessiade. Nesse exemplo iremos escolher: **Newer Clone**
   6. Leia o aviso com atenção e após selecione a caixa **Confirm**.
   7. Verifique tudo e clique em Rollback.
   8. Aguarde o processo terminar.
   9. Verique se os dados foram recuperados conforme desejado.
   10. Em alguns casos pode ser necessário reiniciar o serviço de compartilhamento para que os arquivos sejam exibidos, nesse exemplo devemos reiniciar o serviço Samba.

***Obs: Você também pode só recurar os arquivos que você precisa sem precisar dar um rollback. No Windows dentro da pasta compartilhada, clique com o botão direito e selecione propriedades, procure por versões antigas e lá vai aparecer os snapshots existentes.***

***Qualquer dúvida consulte a documentação (https://www.truenas.com/docs/scale/scaletutorials/dataprotection/periodicsnapshottasksscale/) e veja esse vídeo também (https://www.youtube.com/watch?v=QIdy6sR0HrI).***

### Alterar idioma e horário

Manter o horário atualizado é bom para evitar problemas em logs, autenticação de dois fatores, agendamento de tarefas e outros motivos. Para isso:

1. acesse a área ***System Settings*** no menu lateral e clique em General.
2. Procure por **localization** e clique em Settings.
3. Selecione o idioma que você que usar no servidor.
4. Selecione qual o modelo e idioma do teclado.
5. Selecione a Timezone de acordo com a sua região.
6. Caso precise você pode alterar o formato de exibição da data.
7. Caso precise você pode alterar o formato de exibição do horário.
8. Verique tudo e clique em Save.

<br>

## Autenticação em 2 fatores (2FA)

Com a 2FA conseguimos manter o sistema mais seguro, pois além de utilizar a senha, será nescessário digitar uma quantidade de digitos vindas de app autenticador externo, exemplo: Microsoft Authenticator, Google Authenticator. Ele utiliza o one-time password (OTP), que gera uma conjunto de números dependendo da hora, assim ambos servidor e telefone, devem estar com as horas sincronizadas. Caso o servidor não esteja, você precisa:

- Acessar o terminal via usuário admin.
```bash
  # utilize o comando para ver a hora do servidor

  sudo hwclock

  # Para alterar a hora utilize o comando abaixo, informando a hora e os minutos

  sudo hwclock --set --date 10:30 --update-drift

  # Verique se a hora foi alterada, reinicie o servidor e veja se a hora atualizada se manteve.
```

Pronto, o servidor esta com a hora sincronizada com o seu celular, agora precisamos ativar a autenticação em dois fatores no sistema, para isso:

1. Acesse a área ***Credentials*** no menu lateral e clique em **two-factor authentication**
2. Será aberta uma nova janela.
3. Você precisa informa a quantidade de digítos que serão gerados, nesse exemplo iremos escolher 6 que é o padrão.
4. Você precisa informar o intervalo em que os numeros serão alterados, nesse exemplo iremos utilizar 30 que é o padrão.
5. Em **Window** podemos estender o tempo de duração dos digítos, nesse exemplo vamos utilizar 0, pois queremos que após cada intervalo seja necessário informar um novo conjunto de digítos.
6. Agora clique o botão **Enable Two-Factor Authenticator**, será exibido um QR Code que deve ser escaneado no app autenticador no celular.
7. Após isso, o 2FA já foi ativado, você pode fazer logout e tentar acessar novamente já informando os digítos.

Caso você precise acessar o servidor via SSH, você também poderá ativar o 2FA nele, para isso:

1. Refaça os passos descritos acima de 1 a 6.
2. Procure pela caixa de seleção **Enable Two-Factor Auth for SSH** e clique nela.
3. Verifique tudo e clique em Save.
4. Acesse a área ***System Settings*** no menu lateral e clique em **Services**.
5. Procure pelo serviço SSH e o ative.
6. Clique em editar o serviço SSH e será aberta uma nova janela.
7. Selecione **Log in as Admin with Password** caso vá acesar com o usuário admin (Não recomendado). 
8. Agora abra um terminal e tente fazer um conexão via ssh.
9.  Após informar a senha do usuário, será necessário informar o código em digitos do app autenticador.

***Obs: Você pode acessar o terminal do servidor via interface gráfica caso não deseje ativar o serviço SSH. Acessando "System Settings/Shell" você tem acesso ao terminal do servidor.***

***Caso esteja com problema em acessar a interface gráfica utilizando 2FA, lembre de verificar se os dispositivos estão com o horário sincronizados, caso não funcione mesmo com o horário sincronizado você pode resetar a senha do usuário admin via terminal no próprio servidor, isso vai desativar o 2FA. Qualquer dúvida consulte a documentação (https://www.truenas.com/docs/scale/scaletutorials/credentials/2fascale/) e assista a esse vídeo (https://www.youtube.com/watch?v=F2skM7h9RUk)***

<br>

## Atualização

A atualização do servidor é uma forma de manter o servidor seguro com as novas correções de bugs, recursos de segurança e novidades no sistema. Para isso:

1. Acesse a área ***System Settings*** no menu lateral e clique em Update.
2. O sistema vai verificar se tem novas atualizações, se tiver vai exibir abaixo.
3. Lembre de fazer o backup de seus dados mais importantes, e garanta que nem um novo dado esta sendo guardado na hora da atualização.
4. Para iniciar a atualização clique em **Download Updates**.
5. Vai aparecer um aviso perguntando se você quer salvar o arquivo de configuração, qualquer dúvida sobre isso consulte a seção [Backup](#backup).
6. Após vai aparecer se você quer somente baixar a atualização ou baixar, aplicar a atualização e reiniciar o sistema, nesse exemplo vamos selecionar em aplicar também.
7. Clique para iniciar o download e aguarde o processo terminar.
8. Quando o servidor reiniciar, acesse e veja se atualização ocorreu da forma correta e se não quebrou nada.

***Obs: Verifique em qual train de atualização você esta utilizando, recomendado estar na release. As outras trains podem ser de testes ou desenvolvimento e trazer vários bugs. Qualquer dúvida consulte (https://www.truenas.com/docs/scale/scaletutorials/systemsettings/updatescale/)***

