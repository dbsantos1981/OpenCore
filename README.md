# Hackintosh com OpenCore
***Tentativa*** de criação de um **Guia de Instalação de um Hackintosh com OpenCore**

Este texto é uma adaptação/tradução livre dos textos que estão no site do [OpenCore](https://dortania.github.io). A maioria das imagens utilizadas eu mesmo fiz durante os meus testes do procedimento, as demais peguei nesse site mesmo.

Tentei reorganizar o conteúdo de modo a tentar seguir uma sequência lógica de ações. Espero ter conseguido. Caso tenha críticas ou sugestões favor relatar.

---
**NÃO TEMOS RESPONSABILIDADE NENHUMA POR QUALQUER DANO OU POR QUALQUER PERDA DE DADOS QUE POSSAM OCORRER AO SEGUIR ESTES PROCEDIMENTOS. FAÇA-O POR SUA PRÓPRIA CONTA E RISCO. SE NÃO SE SENTE CAPAZ, PROCURE AJUDA. NÃO INICIE O PROCESSO ANTES DE LER TODO ESTE DOCUMENTO.**

**É OBRIGATÓRIO CONHECER AS INFORMAÇÕES SOBRE SEU HARDWARE, SEM ELAS ESSE PROCEDIMENTO NÃO FAZ SENTIDO**

---

## 1 - Verifique o seu Hardware

Tente localizar seu hardware nas listas abaixo e/ou entenda como é que funciona a coisa. No caso, vou escolher a versão do MacOs baseado no meu chipset de vídeo (para ter menos trabalho). Por exemplo, a minha placa de Vídeo é a Intel HD 3000 (*Legacy Intel*), então vou usar o HighSierra.

- [Compatibilidade de Hardware](https://github.com/dortania/OpenCore-Desktop-Guide/blob/master/extras/hardware.md)
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

### 4.1 - Monte a partição EFI

Para configurar o EFI do pendrive, precisamos montar a partição. Para isso vamos usar o programa [MountEFI](https://github.com/corpnewt/MountEFI). Acesse o link, baixe (clone) o projeto, e execute o programa **MountEFI.command**.

![Screenshot](img/MountEFI1.png)
---

Ele listará os discos conectados, cabe a nós escolhermos o pendrive. No meu caso será a opção 2. 

![Screenshot](img/MountEFI2.png)
---

Ele vai carregar a partição EFI. Nesse momento ela deve estar vazia ainda.

![Screenshot](img/MountEFI3.png)
---

Pode fechar o terminal com o MountEFI.

### 4.1 - Configurando a partição EFI

Baixe o [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg/releases/). Para configurar a estrutura de pastas do OpenCore, você deve pegar a pasta EFI encontrada nas versões do OpenCorePkg e copiá-la para a raiz da sua partição EFI. Nesse exemplo estou baixando a versão 0.5.9 (OpenCore-0.5.9-RELEASE.zip)

![Screenshot](img/ConfigEFI1.png)
---

![Screenshot](img/ConfigEFI2.png)
---

No caso, vemos que ele vem com vários arquivos na pasta Drivers and Tools, conforme print abaixo:

![Screenshot](img/ConfigEFI3.png)
---

Precisamos remover os arquivos que não são necessários agora. Deixe as pastas Drivers e Tools conforme o print abaixo, não mexa nas outras pastas:

![Screenshot](img/ConfigEFI4.png)
---

Agora podemos colocar os drivers de firmware necessários (.efi) na pasta Drivers, e os Kexts / ACPI em suas respectivas pastas. Observe que os drivers UEFI do Clover não são suportados pelo OpenCore (EmuVariableUEFI, AptioMemoryFix, OsxAptioFixDrv, etc). Consulte a [conversão do driver de firmware Clover](https://github.com/dortania/OpenCore-Desktop-Guide/blob/master/clover-conversion/clover-efi.md) para obter mais informações sobre os drivers suportados e aqueles mesclados no OpenCore.

Lembrando:

- SSDTs e DSDTs(.aml) vão para a pasta ACPI
- Kexts(.kext) vão para a pasta Kexts
- Firmware drivers(.efi) vão para a pasta Drivers

### 4.2 Baixando os kexts e EFIs necessários:

Vá para [Gathering Files](https://dortania.github.io/OpenCore-Desktop-Guide/ktext.html) para obter os kexts e drivers de firmware necessários.

Consulte a seção de [hardware suportado](https://github.com/dortania/OpenCore-Desktop-Guide/blob/master/extras/hardware.md) para ter uma ideia melhor do que o macOS requer para inicializar, o suporte de hardware entre o Clover e o OpenCore é bastante semelhante.

#### 4.2.1 Firmware

Estes são os drivers usados pelo OpenCore, para a maioria dos sistemas, você só precisa de 2 drivers .efi para entrar em funcionamento:

- **HfsPlus.efi**

O [HfsPlus.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi) é necessário para ver volumes HFS (ou seja, partições/imagens do instalador do macOS ou partições Recovery). Não misture ele com outros drivers HFS.

- **OpenRuntime.efi**

Substituição do [AptioMemoryFix.efi](https://github.com/acidanthera/AptioFixPkg), usada como uma extensão do OpenCore para ajudar a corrigir o boot.efi das correções da NVRAM e melhorar o gerenciamento de memória.

***Para usuários de hardware mais antigo***

- **OpenUsbKbDxe.efi**

O [OpenUsbKbDxe.efi](https://github.com/acidanthera/OpenCorePkg/releases) é usado para o seletor OpenCore em sistemas legados executando o DuetPkg, não recomendado e até prejudicial para UEFI (Ivy Bridge e mais recente)

- **NvmExpressDxe.efi**

O [NvmExpressDxe.efi](https://github.com/acidanthera/OpenCorePkg/releases) é usado para Haswell e versões mais antigas quando nenhum driver NVMe está embutido no firmware, não é necessário se você não estiver usando uma unidade NVMe

- **XhciDxe.efi**

O [XhciDxe.efi](https://github.com/acidanthera/OpenCorePkg/releases) é sado para Sandy Bridge e versões mais antigas quando nenhum driver XHCI está embutido no firmware, não é necessário se você não estiver usando uma placa de expansão USB 3.0

- **HfsPlusLegacy.efi**

O [HfsPlusLegacy.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlusLegacy.efi) é uma variante herdada do HfsPlus, usada para sistemas que não possuem suporte à instrução RDRAND. Isso geralmente é visto na Sandy Bridge e em versões mais antigas. Para obter uma lista completa dos drivers compatíveis, consulte o item 11.2 na [Documentação em PDF do OpenCorePkg](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Configuration.pdf).

**Esses arquivos devem ser gravados na pasta Drivers da sua partição EFI.**

#### 4.2.2 Kexts

Um kext é uma extensão do kernel. Você pode pensar no kext como um driver para o macOS. Esses arquivos devem ir para a pasta Kexts na sua EFI. Verifique quais deles voê realmente precisa, de acordo com o seu hardware. Por isso que é ***imperativo conhecer bem o seu hardware***. Consulte o [Kexts.md](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Kexts.md) para obter uma lista completa dos kexts suportados.

***Nota do Windows e Linux:***

Kexts se parecerá com pastas normais no seu sistema operacional, verifique se a pasta que você está instalando tem uma extensão .kext visível (e não a adicione manualmente, se estiver faltando). Todos os kext listados abaixo podem ser encontrados pré-compilados no [Repositório Kext](https://onedrive.live.com/?authkey=%21APjCyRpzoAKp4xs&id=FE4038DA929BFB23%21455036&cid=FE4038DA929BFB23). Kexts aqui são compilados toda vez que há um novo commit.

**ESSES 2 KEXTS SÃO OBRIGATÓRIOS**

- VirtualSMC

O kext [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases) emula o chip SMC encontrado em macs reais, sem esse kext seu Hackintosh não inicializa. Uma alternativa ao VirtualSMC é o FakeSMC, que pode ter suporte melhor ou pior. Ele é comumente usado em hardware legado.

- Lilu

O kext [Lilu](https://github.com/vit9696/Lilu/releases) é um kext usado para corrigir muitos processos, necessário para AppleALC, WhateverGreen, VirtualSMC e muitos outros kexts. Sem o Lilu, eles não vão funcionar.


**VirtualSMC Plugins**

- SMCProcessor.kext

Usado para monitorar a temperatura da CPU, não funciona em sistemas baseados em CPU AMD

- SMCSuperIO.kext

Usado para monitorar a velocidade do cooler, não funciona em sistemas baseados em CPU AMD

- SMCLightSensor.kext

Usado para o sensor de luz ambiente em laptops, quem usa desktops não precisa utilizar. Se seu notebook não tem esse sensor, também não use.

- SMCBatteryManager.kext

Usado para medir as leituras de bateria em laptops, quem usa desktops não precisa utilizar. Se você usa seu notebook sem bateria, também não use.

**Placa de Vídeo**

- WhateverGreen

O kext [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases)Usado para correção de gráficos DRM, boardID, correções de buffer de quadros etc., todas as GPUs se beneficiam desse kext. Observe que o arquivo *SSDT-PNLF.dsl* incluído é necessário apenas para laptops e computadores All-In-One. Consulte [Introdução à ACPI](https://dortania.github.io/Getting-Started-With-ACPI/) para obter mais informações.

**Placa de Som**

- AppleALC

O kext [AppleALC](https://github.com/acidanthera/AppleALC/releases) é usado para patch AppleHDA, usado para fornecer áudio integrado. A AMD 15h/16h pode ter problemas com esse kext e os sistemas Ryzen/Threadripper raramente têm suporte para microfone.

**Placa de Rede Ethernet**

- IntelMausi

O kext [IntelMausi](https://github.com/acidanthera/IntelMausi/releases) é necessário para placas de rede Intel. Se sua placa usa os chipsets baseados no I211, você precisará do kext SmallTreeIntel82576.

- SmallTreeIntel82576

O kext [SmallTreeIntel82576](https://github.com/khronokernel/SmallTree-I211-AT-patch/releases) é necessário para as placas de rede Intel I211, baseadas no kext SmallTree (mas corrigidas para oferecer suporte à I211), necessário para a maioria das placas AMD que usam placas de rede Intel.

- AtherosE2200Ethernet

O kext [AtherosE2200Ethernet](https://github.com/Mieze/AtherosE2200Ethernet/releases) é necessário para as placas de rede Atheros e Killer

- RealtekRTL8111

O kext [RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X/releases) é necessário para placas Gigabit Ethernet da Realtek

- LucyRTL8125Ethernet

O kext [LucyRTL8125Ethernet](https://github.com/Mieze/LucyRTL8125Ethernet) é necessário para placas Ethernet de 2,5 Gb da Realtek

**USB**

- USBInjectAll

O [USBInjectAll](https://github.com/Sniki/OS-X-USB-Inject-All/releases) é usado para injetar controladores USB Intel em sistemas sem portas USB definidas na ACPI. Não é necessário no Skylake e mais recente (exceção: as placas AsRock precisam dele). Não funciona em todas as CPUs AMD. 

- XHCI-unsupported

O [XHCI-unsupported](https://github.com/RehabMan/OS-X-USB-Inject-All) é necessário para controladores USB não nativos. Os sistemas baseados em CPU AMD não precisam dele. Chipsets comuns que precisam dele:

* H370
* B360
* H310
* Z390 (não é necessário no Mojave e mais recente)
* X79
* X99
* Placas AsRock (especificamente nas placas-mãe Intel, basicamente todas as placas)

**Wi-fi e Bluetooth**

- AirportBrcmFixup

O [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup/releases) é usado para corrigir placas Broadcom que não sejam da Apple, ele não funcionará na Intel, Killer, Realtek, etc.

- BrcmPatchRAM

O [BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM/releases) é usado para fazer upload de firmware no chipset Broadcom Bluetooth, necessário para todas as placas que não sejam da Apple / Fenvi Airport. Deve ser usado em conjunto com o BrcmFirmwareData.kext.

  - BrcmPatchRAM3 para 10.14+ (em conjunto com BrcmBluetoothInjector)
  - BrcmPatchRAM2 para 10.11-10.14
  - BrcmPatchRAM para 10.10 ou mais antigo
  
A ordem no Kernel -> Adicionar deve ser:

1. BrcmBluetoothInjector
2. BrcmFirmwareData
3. BrcmPatchRAM3

**Kexts específicos para CPUs AMD**

- Gerenciamento de Energia

Há uma solução melhor que o *NullCPUPowerManagment*, conhecida como **DummyPowerManagement**, encontrada em Kernel -> Quirks no config.plist. Isso será abordado em um outro momento.

- XLNCUSBFIX

O [XLNCUSBFIX](https://cdn.discordapp.com/attachments/566705665616117760/566728101292408877/XLNCUSBFix.kext.zip) é um fix para USB em sistemas AMD FX, não é recomendada para Ryzen.

- VoodooHDA

O [VoodooHDA](https://sourceforge.net/projects/voodoohda/) é usado em sistemas FX e o suporte Mic + Audio do painel frontal para o sistema Ryzen, não pode ser usado em conjunto com o AppleALC. A qualidade do áudio é notavelmente pior que a AppleALC nos processadores Zen.

**Extras**

- AppleMCEReporterDisabler

O [AppleMCEReporterDisabler](https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip) útil iniciando o sistema do Catalina para desativar o AppleMCEReporter kext, o que causará kernel panic em CPUs AMD e sistemas de soquete duplo. SMBIOS afetados:

1. MacPro6,1
2. MacPro7,1
3. iMacPro1,1

- VoodooTSCSync

O [VoodooTSCSync](https://bitbucket.org/RehabMan/VoodooTSCSync/downloads/) é necessário para sincronizar o TSC em algumas das placas-mãe Intel HEDT e placas Intel de Servidores. Sem ele o macOS pode ficar extremamente lento ou até mesmo nem inicializar. Skylake-X deve usar  [TSCAdjustReset](https://github.com/interferenc/TSCAdjustReset).

- TSCAdjustReset

No Skylake-X, muitos firmwares, incluindo Asus e EVGA, não gravam o TSC em todos os núcleos. Portanto, precisaremos redefinir o TSC num cold boot e inicializar novamente. A versão compilada pode ser encontrada aqui [TSCAdjustReset.kext](https://github.com/dortania/OpenCore-Desktop-Guide/blob/master/extra-files/TSCAdjustReset.kext.zip). Observe que você deve abrir o kext (ShowPackageContents no localizador, Conteúdo -> Info.plist) e alterar o Info.plist -> IOKitPersonalities -> IOPropertyMatch -> IOCPUNumber para o número de threads de CPU que você possui a partir de 0 (i9 7980xe 18 núcleo seria 35, pois possui um total de 36 threads).

- NVMeFix

O [NVMeFix](https://github.com/acidanthera/NVMeFix/releases) deve ser usado para corrigir o gerenciamento de energia e a inicialização em um NVMe que não seja da Apple, requer o macOS 10.14 ou mais recente.

**Kexts específicos para laptop**

- VoodooPS2

O [VoodooPS2](https://github.com/acidanthera/VoodooPS2/releases) é necessário para sistemas com teclados e trackpads PS2. Os usuários do trackpad devem usá-lo em conjunto com o [VoodooInput](https://github.com/acidanthera/VoodooInput/releases) (deve ser incluído antes do VoodooPS2 em seu config.plist)

- VoodooI2C

O [VoodooI2C](https://github.com/alexandred/VoodooI2C/releases) é usado para corrigir dispositivos I2C, encontrados em alguns touchpads e máquinas de tela sensível ao toque mais sofisticados. Devem ser usados em conjunto com um desses plugins:

1. VoodooI2CHID - Implementa a especificação do dispositivo Microsoft HID.
2. VoodooI2CElan - Implementa suporte para dispositivos proprietários da Elan. (não funciona no ELAN1200 +, use o HID)
3. VoodooI2CSynaptics - Implementa o suporte aos dispositivos proprietários da Synaptic.
4. VoodooI2CFTE - Implementa o suporte ao touchpad FTE1001.
5. VoodooI2CUPDDEngine - Implementa o suporte ao driver Touchbase.

Para descobrir que tipo de teclado e trackpad você possui, verifique o Gerenciador de dispositivos no Windows ou a entrada `dmesg | grep` no Linux.

- NoTouchID

O [NoTouchID](https://github.com/al3xtjames/NoTouchID/releases) é recomendado para SMBIOS que incluem um sensor TouchID para corrigir problemas de autenticação.

### 4.3 SSDTs e ACPI

Então você vê todos esses SSDTs na pasta *AcpiSamples* e se pergunta se precisa de algum deles. Na seção *ACPI* do config.plist veremos quais SSDTs nós precisamos, pois eles são específicos para cada plataforma. A [introdução ao ACPI](https://dortania.github.io/Getting-Started-With-ACPI) possui uma seção estendida sobre SSDTs, incluindo a compilação em diferentes plataformas.

Caso seu sistema seja **IvyBridge** ou **IvyBridge-E** execute este [script](https://github.com/Piker-Alpha/ssdtPRGen.sh) após a instalação do Hackintosh.

***Um rápida explicação sobre a ACPI***

Então, o que são DSDTs e SSDTs? Bem, estas são tabelas presentes no firmware que descrevem dispositivos de hardware como controladores USB, threads de CPU, controladores incorporados, relógios do sistema e outros. Uma DSDT (Tabela de descrição diferenciada do sistema) pode ser vista como o corpo que mantém a maioria das informações, com bits menores de informações sendo transmitidos pelo SSDT (Tabela de descrição do sistema secundário). Você pode pensar no DSDT como os projetos de construção, com os SSDTs sendo notas adesivas que descrevem detalhes adicionais ao projeto.

Você pode ler mais sobre a ACPI e suas especificações no [Manual da ACPI 6.3](https://uefi.org/sites/default/files/resources/ACPI_6_3_May16.pdf)

Então, por que nos preocupamos com essas tabelas? O macOS pode ser muito exigente quanto aos dispositivos presentes no DSDT e, portanto, nosso trabalho é corrigí-lo. Os principais dispositivos que precisam ser corrigidos para que o macOS funcione corretamente são:

1. Embedded controllers(EC)

Todas as máquinas intel semi-modernas têm um EC (geralmente chamado H_EC, ECDV, EC0, etc ...) exposto em seu DSDT, e muitos sistemas AMD também tem isso. Esses controladores geralmente não são compatíveis com o macOS e podem causar kernel panic; portanto, eles precisam estar ocultos no macOS. O macOS Catalina exige que um dispositivo chamado EC esteja presente, nesse caso, um EC fictício é criado.

Nos laptops, o controlador incorporado real ainda precisa estar ativado para que a bateria e as teclas de atalho funcionem, e renomear o EC pode causar problemas adicionais com o Windows, portanto, é preferível criar um EC falso sem desativar o controlador incorporado real.

2. Plugin Type

Isso permite o uso do XCPM, que fornece gerenciamento de energia da CPU nativa na Intel Haswell e em CPUs mais recentes, o SSDT se conectará ao primeiro segmento da CPU. Não destinado a CPUs AMD.

3. AWAC system clock

Isso se aplica a todas as placas-mãe da série 300, incluindo muitas placas Z370, o problema específico é que as placas mais recentes são fornecidas com o relógio AWAC ativado. Esse é um problema porque o macOS não pode se comunicar com os relógios AWAC, portanto, é necessário forçar o relógio RTC herdado ou, se indisponível, criar um falso para o macOS usar.

4. NVRAM SSDT

As verdadeiras placas-mãe da série 300 (não Z370) não declaram o chip FW como MMIO na ACPI e, portanto, o kernel ignora a região MMIO declarada pelo mapa de memória UEFI. Este SSDT traz de volta o suporte NVRAM.

5. Backlight SSDT

Usado para corrigir o suporte ao controle de luz de fundo em laptops.

6. GPIO SSDT

Usado para criar um esboço para permitir a conexão do VoodooI2C, apenas para laptops.

7. XOSI SSDT

Usado para redirecionar chamadas OSI para esse SSDT, usado principalmente para enganar nosso hardware (faaz ele pensar que está inicializando o Windows) para obter um melhor suporte ao trackpad. Caso ele interrompa a inicialização do Windows, use o GPIO SSDT. O uso do XOSI não será abordado neste guia.

8. Patches IRQ SSDT e ACPI

Necessário para corrigir conflitos de IRQ no DSDT, principalmente para laptops. Exclusivo no caso do SSDTTime. Nota: *Skylake e sistemas mais novos raramente têm conflitos de IRQ, isso é predominante principalmente em Broadwell e em sistemas mais antigos*.

Agora vá para a próxima sessão, para saber quais SSDTs seu sistema precisa.

#### 4.3.1 Escolhendo os SSDTs - De qual SSDT seu hardware precisa?

Por favor, veja a seção ACPI específica do seu config.plist, todos os SSDTs necessários tem uma breve explanação. Abaixo temos um resumo:

- Desktop

Plataforma | CPU	| EC | AWAC |	NVRAM	| USB
:---:|:---:|:---:|:---:|:---:|:---:|
Ivy Bridge|[CPU-PM](https://dortania.github.io/OpenCore-Desktop-Guide/post-install/pm.html#sandy-and-ivy-bridge-power-management) (Rodar após a Instalação)|[SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|N/A|N/A|N/A|
Ivy Bridge-E|[CPU-PM](https://dortania.github.io/OpenCore-Desktop-Guide/post-install/pm.html#sandy-and-ivy-bridge-power-management) (Rodar após a Instalação)|[SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|N/A|N/A|N/A|
Haswell/Broadwell|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|N/A|N/A|N/A|
Haswell-E|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|N/A|N/A|N/A|
Broadwell-E|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|N/A|N/A|N/A|
Skylake|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|N/A|N/A|N/A|
Skylake-X|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|N/A|N/A|N/A|
KabyLake|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|N/A|N/A|N/A|
CoffeeLake|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|[SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html)|[SSDT-PMC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/nvram.html)|N/A|
CometLake|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|[SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html)|[SSDT-PMC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/nvram.html)|[SSDT-RHUB](https://dortania.github.io/Getting-Started-With-ACPI/Universal/rhub.html)|
AMD (15/16/17h)|N/A|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|N/A|N/A|N/A|
---

- Laptop

Plataforma | CPU	| EC | Backlight | I2C TrackPad | AWAC | USB | IRQ |
:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
Ivy Bridge | [CPU-PM](https://dortania.github.io/OpenCore-Desktop-Guide/post-install/pm.html#sandy-and-ivy-bridge-power-management) (Rodar após a Instalação)	| [SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)	| [SSDT-PNLF](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html)	| [SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html)	| N/A |	N/A	| [IRQ SSDT](https://dortania.github.io/Getting-Started-With-ACPI/Universal/irq.html) |
Haswell|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|[SSDT-PNLF](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html)|[SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html)| N/A |	N/A	| [IRQ SSDT](https://dortania.github.io/Getting-Started-With-ACPI/Universal/irq.html) |
Broadwell|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|[SSDT-PNLF](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html)|[SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html)| N/A |	N/A	| [IRQ SSDT](https://dortania.github.io/Getting-Started-With-ACPI/Universal/irq.html) |
Skylake|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|[SSDT-PNLF](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html)|[SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html)| N/A |	N/A	| N/A |
KabyLake|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|[SSDT-PNLF](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html)|[SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html)| N/A |	N/A	| N/A |
CoffeeLake (8th Gen) | [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|[SSDT-PNLF-CFL](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html)|[SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html)| N/A |	N/A	| N/A |
CoffeeLake (9th Gen) | [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|[SSDT-PNLF-CFL](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html)|[SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html)| [SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html) |	N/A	| N/A |
CometLake  | [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|[SSDT-PNLF-CFL](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html)|[SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html)| [SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html)|N/A|N/A|
IceLake|[SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html)|[SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)|[SSDT-PNLF-CFL](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html)|[SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html)| [SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html)|	[SSDT-RHUB](https://dortania.github.io/Getting-Started-With-ACPI/Universal/rhub.html)	| N/A |
---

#### 4.3.2 Como criar as SSDTs

A criação de SSDTs se enquadra principalmente em 3 campos:

- Pre-Built SSDTs (pré-construídos) - Eles funcionam, mas não são perfeitos.
- Automated Tools (Ferramentas automatizadas) - Visto principalmente com o SSDTTime, funciona muito melhor, embora não possa cobrir todas as situações.
- Manually (Criando-os manualmente) - Sempre funcionará, será muito mais limpo e você aprenderá sobre o processo.

#### 4.3.2.1 Pre-Built SSDTs

- Power Management
- Embedded Controllers
- Trackpad
- Backlight
- System Clock(AWAC)
- NVRAM(PMC)

Essa seção é principalmente para usuários que estão tendo problemas para compilar, descompilar ou mesmo para ter a compreensão geral do processo da ACPI. Os principais motivos para você evitar os *prebuilts* são os seguintes:

- Processo de boot muito lento
  - Principalmente porque esses SSDTs precisam passar por todos os caminhos possíveis
  - Curiosidade: o SSDT-EC-DESKTOP pré-construído é 42 vezes maior do que criar o arquivo você mesmo 
- Nem sempre funcionam
  - Os SSDTs fornecidos podem lidar apenas com tantas situações, e alguns podem de fato não funcionar
  - Visto principalmente com o SSDT-AWAC, pois pressupõe que há uma maneira de o RTC ser facilmente ativado
- Você não aprende nada assim
  - Esse é o maior problema, pois você não tem idéia real de como solucionar problemas ou como tornar esses arquivos mais avançados.
  
**Power Management**

Para Haswell e mais recentes: [SSDT-PLUG-DRTNIA](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PLUG-DRTNIA.aml)

Para Ivy Bridge e versões mais antigas, consulte a página [Otimizando o gerenciamento de energia](https://dortania.github.io/OpenCore-Desktop-Guide/post-install/pm.html). Isso será feito após a instalação. 

Os usuários de CPU AMD não precisam de SSDTs para gerenciamento de energia.

**Embedded Controllers**

**Desktops**

- [SSDT-EC-USBX-DESKTOP](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-EC-USBX-DESKTOP.aml) -> Para Skylake e mais recentes, e também todos os sistemas da AMD.

- [SSDT-EC-DESKTOP](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-EC-DESKTOP.aml) -> Para Broadwell e mais antigos.

**Laptops**

- [SSDT-EC-USBX-LAPTOP](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-EC-USBX-LAPTOP.aml) -> Para Skylake e mais recentes.

- [SSDT-EC-LAPTOP](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-EC-LAPTOP.aml) -> Para Broadwell e mais antigos.

**Trackpad**

Usado para ativar os recursos que são exclusivos do Windows no macOS, observe que os patches abaixo provavelmente interromperão a inicialização do Windows via OpenCore. É altamente recomendável que você faça um você mesmo para evitar problemas futuros abaixo da linha [Trackpad GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html)

- [SSDT-XOSI](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-XOSI.aml)

- [XOSI-Rename.plist](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/XOSI-Rename.plist)

Observe que você precisará adicioná-lo ao seu config.plist em *ACPI -> Patch*:

Comment	| String	| Change _OSI to XOSI
Enabled | Boolean | YES
Count	| Number	| 0
Limit	| Number	| 0
Find	| Data	| 5f4f5349
Replace	| Data| 584f5349

**Backlight**

Para laptops e desktops do tipo *all-in-one*, isso corrige o suporte a controle de brilho.

[SSDT-PNLF](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PNLF.aml) -> Para KabyLake e anteriores.

[SSDT-PNLF-CFL](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PNLF-CFL.aml) -> Para Coffee Lake e mais recentes.

**System Clock(AWAC)**

Necessário para sistemas mais novos que executam relógios de sistema baseados em AWAC, principalmente relevantes para o Z390 e mais recente (Gigabyte e AsRock, no entanto, o suportaram no Z370 com uma atualização do BIOS).

- [SSDT-AWAC](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-AWAC.aml)

O maior problema desse prebuilt é que estamos supondo que os dois tenham um relógio AWAC e que ele possa ser desativado. É altamente recomendável que você faça um você mesmo para saber realmente se ambos precisam disso e se vai funcionar: [AWAC vs RTC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html)

**NVRAM(PMC)**

Necessário para trazer de volta o suporte NVRAM para Z390 e mais recente, NÃO USE NO Z370 ou mais antigo.

Nota: As desktop boards Z490 parecem não precisar disso, mas se você estiver com problemas de NVRAM, poderá tentar este SSDT.

[SSDT-PMC](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PMC.aml)

Lembre-se de que apenas estas placas precisam desse SSDT:

1. B360
2. B365
3. H310
4. H370
5. Z390
6. 400 series (Comet Lake)
7. 495 series (Icelake)

**USB(RHUB)**

Necessárioi para a 10ª geração de CPUs. Placas Gigabyte and AsRock não precisam dele

[SSDT-RHUB.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-RHUB.aml)

#### 4.3.2.2 SSDTs: o caminho mais fácil

Então, aqui vamos usar uma ferramenta super simples feita pela ***CorpNewt***: [SSDTTime](https://github.com/corpnewt/SSDTTime)

O que essa ferramenta faz é um dump do DSDT do firmware do seu computador e em seguida cria as SSDTs com base nesse DSDT coletado.

Isso deve ser feito na máquina de destino executando Windows ou Linux, ou seja, na máquina que você quer que seja o seu Hackintosh.

**O que o SSDTTime não pode fazer:**

- HEDT SSDTs -> Nessa plataforma o ACPI não é reconhecido de maneira correta. Isto inclui as placas X79, X99 e X299. Então pode ser usado um [prebuilt](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)

- Laptop EC fix -> Isto ocorre porque não se quer que o EC do laptop seja desligado. Então pode ser usado um [prebuilt](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html)

- SSDT-PNLF -> Precisa ser configurado para o seu sistema

- SSDT-GPI0 -> Precisa ser configurado para o seu sistema

- AWAC and RTC0 SSDTs -> As placas Intel da série 300 precisarão fazer essa configuração fora (os sistemas Z390 são mais comuns para exigir isso, mas alguns Gigabyte Z370 também)

- PMC SSDT -> Para corrigir *300 series Intel NVRAM*. Então pode ser usado um [prebuilt](https://dortania.github.io/Getting-Started-With-ACPI/Universal/nvram.html)

- USBX SSDT -> Está incluído nos SSDTs de amostra, mas o SSDTTime apenas faz parte do SSDT-EC, para Skylake e mais recentes pode-se usar [prebuilt](https://github.com/dortania/USB-Map-Guide/blob/master/extra-files/SSDT-USBX.aml)

- RHUB SSDT -> Se você tiver uma CPU de 10a geração, precisará usar o prebuilt ou criá-lo manualmente.

Para usuários que não possuem todas as opções disponíveis no SSDTTime, siga a seção "SSDTs: O longo caminho". Mesmo assim você ainda pode usar o SSDTTime para SSDTs compatíveis.

**Executando o SSDTTime**

No Windows, execute o arquivo *SSDTTime.bat* como administrador na máquina em que você quer fazer o Hackintosh. Aparecerá uma janela similar a essa:

![Screenshot](img/ssdttime1.png)

O que essas opções fazem:

1. FixHPET - Corrigir conflitos de IRQ -> Correção de IRQ, necessária principalmente para usuários de X79, X99 e laptop (use a opção C para omitir IRQs herdados e conflitantes)
2. FakeEC - Fake EC com reconhecimento de SO -> Este é o SSDT-EC, necessário para usuários da Catalina
3. PluginType - Define plugin-type = 1 na CPU0 / PR00 -> Este é o SSDT-PLUG, apenas para Intel
4. Dump DSDT - Faz um dump automaticamente do DSDT do seu sistema

O que queremos fazer é selecionar a opção 4. Fazer o dump do DSDT primeiro e, em seguida, selecionar as opções apropriadas para o seu sistema.

E o USBX? Para Skylake e mais recentes, além da AMD, você pode pegar um prebuilt aqui: [SSDT-USBX.aml](https://github.com/dortania/USB-Map-Guide/blob/master/extra-files/SSDT-USBX.aml). Este arquivo é plug and play e não requer configuração do dispositivo, não use no Broadwell e mais antigo.

Nota sobre solução de problemas: consulte [Solução de problemas](https://dortania.github.io/OpenCore-Desktop-Guide/troubleshooting/troubleshooting.html) gerais, se você estiver tendo problemas ao executar SSDTTime.

#### 4.3.2.3 Adicionando ao OpenCore

Não esqueça que os SSDTs precisam ser adicionados ao Opencore, lembrando de que .aml é um arquivo compilado, enquanto que .dsl é código. Adicione apenas o arquivo .aml:

- EFI/OC/ACPI
- config.plist -> ACPI -> Add

Lembre-se de que Cmd / Crtl + R com o OwnTree apontado para sua pasta OC adicionará todos os seus SSDTs, kexts e drivers .efi à configuração para você. Não adicione seu DSDT ao OpenCore, ele já está no seu firmware. Se você não tiver certeza do que isso se refere, volte ao guia OpenCore e selecione sua configuração com base na arquitetura da sua CPU.

Para aqueles que ainda não possuem um config.plist, convém voltar para os respectivos guias do OpenCore e criar o config.plist:

- Guia do OpenCore Desktop
- Guia do laptop OpenCore

Os usuários do FixHPET também precisarão mesclar oc_patches.plist em seu config.plist. Para fazer isso:

- Abra os dois arquivos
- Exclua a seção ACPI -> Patch do config.plist
- Copie a seção ACPI -> Patch de patches.plist
- Cole onde os patches antigos estavam no config.plist

### 4.3.3 O caminho longo para as SSDTs

Infelizmente, algumas coisas não são tratadas pelo SSDTTime, portanto, não tenha medo, pois fazer SSDTs é super fácil. O processo básico é:

- Dump do DSDT (o dump que o SSDTTime fez serve)
- Descompilar o DSDT
- Faça as SSDTs com base nesse DSDT (você precisará do MaciASL ou de um editor de texto)
- Compilar SSDTs

Agora continue e domine os caminhos da ACPI!

#### 4.3.3.1 Obtendo uma cópia do nosso DSDT

Então, para começar, precisamos obter uma cópia do seu DSDT no firmware. A maneira mais fácil é pegar o DSDT.aml feito pelo SSDTTime. Aqui estão algumas outras opções:

**A partir do Windows:**

- SSDTTime

  - Escolha a opção 4. Dump DSDT - Despeja automaticamente o DSDT do sistema

- ou então o **acpidump.exe**

No prompt de comando, execute: acpidump.exe -b -n DSDT -z, isso fará o dump do DSDT como um arquivo .dat. Renomeie isso para DSDT.aml
Observe que todos os patches ACPI do Clover/OpenCore serão aplicados ao DSDT com os 2 métodos acima.

**A partir do Linux:**

- SSDTTime

  - Escolha a opção 4. Dump DSDT - Despeja automaticamente o DSDT do sistema
  
Observe que todos os patches ACPI do Clover/OpenCore serão aplicados ao DSDT com o método acima.

**A partir do Clover**

Para aqueles com o Clover instalado anteriormente, esta é uma maneira simples de obter suas tabelas ACPI:

Pressione F4 no menu Inicialização do Clover. O DSDT pode ser encontrado em EFI / CLOVER / ACPI / origin, esta pasta deve ser criada antes de realizar o dumping.

**A partir do OpenCore**

Com o OpenCore 0.5.9, temos uma nova peculiaridade chamada SysReport, que realmente despeja nosso DSDT automaticamente ao acessar a tela de inicialização. Os principais problemas são:

- Você já precisa de um OpenCore USB inicializável para obter esse despejo
- Isso também requer uma versão DEBUG do 0.5.9

Para o último, você só precisa substituir os seguintes arquivos pela [versão DEBUG](https://github.com/acidanthera/OpenCorePkg/releases):

- EFI/BOOT/BOOTx64.efi
- EFI/OC/Bootstrap/Bootstrap.efi
- EFI/OC/Drivers/OpenRuntime.efi
- EFI/OC/OpenCore.efi

No primeiro, você pode realmente pular a seção ACPI, retornar ao guia OpenCore ([Desktop](https://dortania.github.io/OpenCore-Desktop-Guide/), [Laptop](https://dortania.github.io/vanilla-laptop-guide/)) e terminar de fazer o USB. Depois de inicializado no seletor, você pode desligar o PC e verificar seu USB:

![ScreenShot](img/sysreport.png)

E pronto! Você tem um DSDT! Agora você pode continuar fazendo SSDTs

#### 4.3.3.2 Compilando e Descompilando Tabelas ACPI

Consulte [Solução de problemas](https://dortania.github.io/Getting-Started-With-ACPI/troubleshooting.html) se você tiver erros de dumping / compilação.

- Compilação e descompilação do macOS

Portanto, compilar DSDTs e SSDTs é bastante fácil com o macOS, tudo o que você precisa é o [MaciASL](https://github.com/acidanthera/MaciASL/releases). Para compilar, apenas File -> SaveAs -> ACPI Machine Language Binary (.AML), a descompilação está apenas abrindo o arquivo no MaciASL.

Para aqueles que preferem a linha de comando, pegue [iasl-stable](https://github.com/acidanthera/MaciASL/blob/master/Dist/iasl-stable) e execute o seguinte:

`caminho/para/iasl.exe caminho/para/DSDT.aml`

Se o arquivo .aml compilado for fornecido, um arquivo .dsl descompilado será fornecido e vice-versa.

![ScreenShot](img/macos-compile.png)

- Compilação e descompilação do Windows

Compilar e descompilar no Windows é bastante simples, porém, você precisará do [iasl.exe](https://acpica.org/downloads/binary-tools) e do Prompt de Comando:

`caminho/para/iasl.exe caminho/para/DSDT.aml`

![ScreenShot](img/windows-compile.png)

Se o arquivo .aml compilado for fornecido, um arquivo .dsl descompilado será fornecido e vice-versa.

- Compilação e descompilação do Linux

Compilar e descompilar com o Linux é tão simples quanto, você precisará de uma cópia do iasl. Você pode obtê-lo [aqui](http://amdosx.kellynet.nl/iasl.zip) ou através do pacote iasl no seu gerenciador de pacotes. iasl pode ser usado como tal:

`caminho/para/iasl caminho/para/DSDT.aml`

Se o arquivo .aml compilado for fornecido, um arquivo .dsl descompilado será fornecido e vice-versa.

### 4.4 Correção para Desktops (desativando GPUs sem suporte (SSDT-GPU-DISABLE))

Isso é necessário principalmente para GPUs que não são suportadas no macOS, principalmente os usuários da Nvidia que desejam emparelhar uma GPU AMD para uso no macOS. Embora o WhateverGreen ofereça suporte ao `boot-arg -wegnoegpu`, isso só funciona quando executado no iGPU; portanto, para o resto de nós, precisamos fazer um SSDT. Portanto, para desativar uma GPU específica, precisamos encontrar algumas coisas:

- Caminho ACPI da GPU
- [SSDT-GPU-DISABLE](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/decompiled/SSDT-GPU-DISABLE.dsl.zip)

**Caminho ACPI da GPU**

Para encontrar o caminho PCI de uma GPU é bastante simples, a melhor maneira de encontrá-lo é executando o Windows:

- Abra o Gerenciador de dispositivos
- Selecione Adaptadores de vídeo, clique com o botão direito do mouse na sua GPU e selecione Propriedades
- Na guia Detalhes, procure "Caminhos de localização"

Observe que algumas GPUs podem estar ocultas em "nome do dispositivo BIOS"

![ScreenShot](img/amd.png)

![ScreenShot](img/nvidia.png)

O segundo "ACPI" é o que queremos:

`ACPI (_SB _) # ACPI (PC02) #ACPI (BR2A) #ACPI (PEGP) #PCI (0000) #PCI (0000)`

Agora, converter isso em um caminho ACPI é bastante simples, remova os #ACPI e #PCI (0000):

`_SB_.PC02.BR2A.PEGP`
E pronto! Encontramos o caminho da ACPI, estamos prontos para continuar.

**Fazendo o SSDT**

Para começar, pegue o nosso exemplo de [SSDT-GPU-DISABLE](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/decompiled/SSDT-GPU-DISABLE.dsl.zip) e abra-o. Mude o seguinte:

`Externo (_SB_.PCI0.PEG0.PEGP, DeviceObj)
Método (_SB.PCI0.PEG0.PEGP._DSM, 4, NotSerialized)
`
Para o nosso exemplo, vamos trocar o seguinte:

- todos *PCI0* para **PC02**
- todos *PEG0* para **BR2A**

Dica: se o caminho da ACPI for um pouco menor que o exemplo, tudo bem. Apenas verifique se os caminhos da ACPI estão corretos para o seu dispositivo. Alguns usuários também podem precisar adaptar `_SB_` ao seu caminho.

Com isso feito, compile o DSDT conforme procedimento já informado.

### 4.5 Correções para Laptops

#### 4.5.1 Backlight (SSDT-PNLF)

O objetivo deste SSDT é criar um dispositivo PNLF para o macOS, especificamente um com um ID de hardware APP0002. Felizmente, o WhateverGreen cuidará do resto do trabalho para nós.

Para a correção da luz de fundo, existem 2 métodos que você pode escolher:

- **Prebuilt**

De longe o método mais fácil, tudo o que você precisa fazer é baixar o seguinte arquivo:

  - [SSDT-PNLF.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PNLF.aml) -> Para a maioria dos usuários

  - [SSDT-PNLF-CFL.aml](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PNLF-CFL.aml) -> Para Coffee Lake e mais recente

Principais coisas a serem observadas com este método:

  - Assume o caminho da GPU, funciona muito bem para 99% dos dispositivos, mas se você estiver com problemas para controlar a luz de fundo, isso pode ser algo a se observar
  - Esse método não ensina nada a você. Para a maioria, isso não importa. Mas, para alguns, saber o que faz o seu hackintosh funcionar faz parte da jornada.

- **Manual**


#### 4.5.2 dd

#### 4.5.3 dd

### 4.6 Correções para Ambos (Desktops e Laptops)
