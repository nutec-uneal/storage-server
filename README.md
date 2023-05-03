# Storage Server

O servidor de armazenamento permite que você centralize seus armazento em somente uma máquina, utilizando o conceito de NAS (Network Attached Storage),com o software TrueNAS.

## Sumário

- [Storage Server](#storage-server)
    - [Sumário](#sumário)
    - [Requisitos](#requisitos)
    - [Instalação](#instalação)
    - [Configuração](#configuração)
        - [Discos](#discos)


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

Documentação TrueNAS: (https://www.truenas.com/docs/)

<br>

## Instalação

Para realizarmos a instalação do TrueNAS precisamos fazer o download da sua Iso no site oficial. Escolha a versão Scale que é baseada no Debian, a versão Core é baseada em FreeBSD. 

Link download versão scale: (https://www.truenas.com/download-truenas-scale/#) 

Após o download queime em um pendrive e dê inicio a instalação.

1. A primeira tela vai aparecer 4 opções, selecione Install/Upgrade.
2. Após ele vai perguntar em qual disco o sistema deve ser instalado, selecione o Ssd.
3. Agora ele vai dar um aviso que vai apagar tudo do disco selecionado, cheque se o disco é o correto e pode prosseguir.
4. Agora é vai pedir a criação de uma senha para o root.
5. Após isso o sistema vai ser instalado, aguarde o processo acabar.
6. Quando encerrar vai abrir o terminal com várias opções e com o ip da máquina para acessar a interface web.
7. Digite o ip no navegador e acesse usando a senha criada.

<br>

## Configuração

### Discos

Após acessar a interface web você precisa configurar os hds em algum tipo de Raid, se você estiver armazenando arquivos importantes recomendo usar algum tipo com redudância. Para isso:

1. Acesse a área de storage no menu lateral.
2. Clique em Create Pool.
3. Você precisa colocar um nome. 
4. Abaixo ele vai exibir quais são os discos disponíveis para usar, selecione os que você vai utilizar.
5. No fim da janela ele vai exibir os discos selecionados e qual o tipo de RAID será usado, você pode alterar clicando em cima na opção.
6. Verifique se tudo esta correto e clique em Create.

Pronto agora você tem uma Pool e pode compartilhar ela atráves da rede. Existe vários protocolos de compartilhamento, selecione o que mais atende as suas necessidades, nesse exemplo será escolhido o Samba (SMB). Para isso:

1. Acese a área de Shares no menu lateral.
2. Será exibido os protocolos disponíveis.
3. Na área SMB clique em Add e será aberta uma nova janela.
4. Informe o caminho da Pool criada anteriormente.
5. Informe um nome para seu compartilhamento.
6. Informe o propósito do compartilhamento, ele varia dependendo da necessidade, nesse caso será escolhido: Private SMB Datasets and Shares.
7. Clique em Enabled para habilitar o compartilhamento.
8. Verifique tudo e clique em Save.

Agora você tem um serviço samba rodando no seu servidor, para testar utilize uma máquina cliente:

- Em Windows -> Abra o Explorer, clique na barra de caminho lá em cima e digite: \\\IP-do-servidor.

- Em Linux -> Acesse seu gerenciador de arquivos, clique barra de caminho lá em cima e digite: smb://IP-do-servidor.

