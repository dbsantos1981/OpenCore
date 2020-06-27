# Hackintosh com OpenCore
***Tentativa*** de criação de um **Guia de Instalação de um Hackintosh com OpenCore**
Este texto é uma adaptação/tradução do texto que esta é 
## 1 - Verifique o seu Hardware

Tente localizar seu hardware nas listas abaixo. No caso, vou escolher a versão do MacOs baseado no meu chipset de vídeo (para ter menos trabalho). Por exemplo, a minha placa de Vídeo é a Intel HD 3000 (*Legacy Intel*), então vou usar o HighSierra.

- [Placas de Vídeo](https://dortania.github.io/GPU-Buyers-Guide/)
- [Placas de Rede Wi-fi](https://dortania.github.io/Wireless-Buyers-Guide/)

## 2 - Faça o Download do MacOs e prepare os arquivos

Acesse o site https://github.com/corpnewt/gibMacOS e faça o download do projeto. Descompacte, entre na pasta e execute o script **gibMacOS.command**

![Screenshot](img/gibMacOSFiles.png)
---
Vai aparecer um menu em que você deve digitar o número equivalente a versão do MacOS que quer baixar. Digite o número, aperte ENTER e aguarde o download.

![Screenshot](img/gibMacOSMenu.png)
---
Quando o download for concluído pode fechar a janela do terminal. Os arquivos estarão na pasta "MacOs Downloads" e dentro dela a pasta referente a versão baixada.

![Screenshot](img/gibMacOSDownloadHighSierra.png)
---
Agora vamos executar o script **BuildmacOSInstallApp.command** ,quando ele abrir, arraste para ele a pasta onde foi feito o download no passo anterior (ou então digite/cole o caminho completo da pasta).

![Screenshot](img/gibMacOSBuil1.png)
---
![Screenshot](img/gibMacOSBuild2.png)
---
![Screenshot](img/gibMacOSBuil3.png)
---
Quando concluir, pode sair deste script. Vai aparecer na pasta um novo arquivo chamado (no meu caso) *Instalação do macOS High Sierra*. Para facilitar a próxima etapa, mova esse novo arquivo para a pasta **Aplicativos**

![Screenshot](img/gibMacOSBuil4.png)
---

## 3 - Crie o pendrive de instalação

Agora, formataremos o pendrive para preparar o instalador do macOS e o OpenCore. Vamos usar o macOS Extended (HFS) com um mapa de partição GUID. Abra o **Utilitário de Disco (Disk Utility)** e selecine o pendrive no lado esquerdo. Clique em **Apagar (Erase)** e escolha conforme a imagem abaixo. Com isto ele criará 2 partições: a partição *MyVolume* como principal e a partição *EFI* (oculta), que será usada como uma partição de inicialização, na qual a sua placa-mãe procurará pelos arquivos de inicialização.

![Screenshot](img/DiskUtility1.png)
---

Com o pendrive formatado, vamos executar um comando para transerir a imagem criada no passo anterior. Esse comando está na página de suporte da [Apple](https://support.apple.com/en-us/HT201372), escolha o comando adequado a versão que está instalando.

![Screenshot](img/CreateInstallMedia1.png)
---

Cole o comando no terminal, digite sua senha, digite 'y' para confirmar. Ao final, o pendrive terá o nome do sistema escolhido. Quando concluir, pode fechar o terminal. Esse comando demora para ser concluído, tenha paciência.

![Screenshot](img/CreateInstallMedia2.png)
---

![Screenshot](img/CreateInstallMedia3.png)
---

## 4 - Configurando o ambiente EFI do OpenCore

dfdfdsf
