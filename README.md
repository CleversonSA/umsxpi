# umsxpi

Um "clone" do MSX 1 e MSX 2 baseado no port do emulador Blue MSX para linux e no fork do Blueberry MSX (https://github.com/matheusjgsantos/BlueberryMSX-2.0plus), usando uma imagem refinada do Raspberry PI OS Lite 32bits e voltado para a utilização da placa adaptadora RPMC (https://github.com/meesokim/msxslot).

Basta queimar a imagem em um cartão SD e usar o emulador do MSX, mensagens do console, comandos linux, logins etc. Diversão direto ao assunto.

# RPMC

O projeto nasceu para a utilização da placa adaptadora de cartuchos de MSX para Raspberry PI, criada por meesokim (https://github.com/meesokim/msxslot) e ser quase um "MSX" mini, ainda que emulado. A placa pode ser montada ou pode ser adquirida no site do Victor Trucco (https://loja.victortrucco.com/msxpi)

# Pré-requisitos

Você irá precisar de:

	- Raspberry PI 3B
  - Placa RPMC
  - Teclado USB qualquer
  - Monitor HDMI
  - Cartão SD (mínimo 8GB)
  - (Opcional) Cartucho de jogo qualquer (somente para testar o carregamento)
  - Fonte 5V de no mínimo 2A (menos que isso vão aparecer mensagens de "undervoltage" e pode não funcionar a contento o conjunto)

# Instalação

Baixe a imagem mais recente no repositório e queime em um cartão SD utilizando o programa de preferência. Se for o Windows, por exemplo, existe o Balena Etcher (https://etcher.balena.io/) que pode fazer esse trabalho. No linux, pode ser usado o comando dd ou o app Disks (distribuições baseadas no Ubuntu). 

Após queimar a imagem do umsxpi no cartão no PC, coloque-o no Raspberry PI, ligue o teclado, a placa RPMC nos pinos da GPIO, conecte o Raspberry PI no monitor HDMI. Por último, a fonte no RPi, logo será inicializado.

O primeiro boot pode demorar até 20-30 segundos e a tela do MSX2 aparecerá para uso com o MSX-DOS carregado.

# Uso

Por default, o MSX inicializado é um MSX2+ genérico, no entanto, os aparelhos que o Blue MSX suportar poderão ser utilizados (ver sessão configurações).

- Para carregar um jogo do cartucho, coloque o cartucho no slot 1 do RPMC e pressione F12 para reiniciar o emulador

- Para retirar o cartucho, retire o cartucho e pressione F12 para reiniciar o emulador e acessar o MSX-DOS. É RECOMENDADO, primeiro desligue o Raspberry PI, retire o cartucho e o ligue novamente. Nos testes que fiz, não precisei disso, no entanto, fica a recomendação.

- Para reiniciar o emulador, pressione F12.

# Limitações

- Por ser uma imagem personalizada, o acesso Wifi e SSH da imagem linux está desativada. Pode ser configurada posteriormente, no entanto, não é o intuito desse projeto. Todavia, se quiser configurar o Wifi para acesso remoto, fique a vontade.
  
Sobre o RPMC tenha em mente que:

- Cartuchos de IO (ex: WozBlaster OPL4, Obsonet...) não funcionam, travam o emulador apesar de parecer que a parte da ROM deles carregam.
- SD Mapper não funciona, inicializa com os leds ligados e trava na inicialização.
- Conforme relatos na comunidade MSX no Facebook (https://www.facebook.com/groups/msxbrasiloficial/?locale=pt_BR), cartuchos de jogos mais elaborados não funcionam com o RPMC.

Em pesquisas, parece que o meesokim implementou os protocolos básicos de comunicação do emulador com os cartuchos, conforme o artigo no MSX Resource Center(https://www.msx.org/forum/msx-talk/hardware/rpmc-raspberry-pi-msx). Fora isso, existe uma limitação da GPIO do RPi no quesito alocação de uso. Se algum processo do linux requisitar um dos pinos do GPIO enquanto o emulador opera, quebra a comunicação com o cartucho, fora a questão dos timings dos sinais.

Além disso:

- Raspberry PIs abaixo de 3B não funciona a imagem. Ou ficam lentos demais ou nem inicializa (testei no Raspberry PI Zero por exemplo).
- Raspberry PI 4, 400 ou superior - Pendente de teste. Não garanto que funcione.
- Pode ser que o SD corrompa após muitos desligues do Raspberry PI (se colocar centenas de vezes, explicado na sessão Desligando o umsxpi)

# Desligando o umsxpi

Até o momento, desligar seria desligar o Raspberry PI da fonte mesmo. No linux, isso não é recomendado, pode corromper o SD (em mais de 10 anos que acompanho RPi, nunca me aconteceu), isso se deve por que o blueberry MSX é lançado como serviço do Linux, muito antes do login no S.O.

Assim, não consigo capturar as entradas do usuário para uma tecla de atalho de desligue. A tecla F12, sai do emulador, e o uso para reinício para não ter que ficar esperando 30 segundos cada reinício.

É uma limitação que estou pesquisando uma solução no momento.

# Configurações:

- Desligue o Raspberry PI e coloque o cartão em um leitor de cartões no PC. A partição boot será utilizada para as configurações.

- Escolhendo a máquina emulada:

 	* Na pasta raiz da partição de boot, crie um arquivo vazio, respeitando maiúsculas e minúsculas, sem nenhuma extensão, conforme a tabela abaixo:
    
   | Nome do arquivo | Máquina emulada |
   | :---: | :---: |
	| msxpimsxexpert | MSX Expert 1.1 Gradiente |
	| msxpimsx2 | MSX 2+ Genérico |

- Acessando o shell e login para configurações avançadas:

 	* Na pasta raiz da partição de boot, crie o arquivo vazio *msxpilogin*, respeitando maiúsculas e minúsculas, sem nenhuma extensão

	* O usuário é *umsxpi* e a senha *umsxpi*
    
	* O emulador está na pasta _/home/umsxpi/BlueBerryMSX-2.0.plus_

	* O serviço que lança o emulador é o _/lib/systemd/system/bluemsx.service_


# Disquetes virtuais (experimental):

Internamente a emulação carrega um disquete com o MSX-DOS 2.11 (está no /home/umsxpi). No entanto, se na partição de boot do cartão SD estiver presente os seguintes arquivos abaixo (respeitar maiúsculas e minúsculas), serão carregados no emulador:

| Nome do arquivo | Drive |
| :---: | :---: |
| diska.dsk | Unidade de disco A |
| diskb.dsk | Unidade de disco B |

Todos os discos virtuais devem estar no formato msx-dos de 720kb. No Windows, use o app Disk-Manager, no caso de dúvidas, tem o vídeo do TByteCreator explicando (https://www.youtube.com/watch?v=ycX79EpJy6g).


# Changelog

- Versão 0.9
  * Imagem inicial voltada para versão 32bits do Raspiberry PI OS
  * Suporte a RPMC
  * Atalhos somente para o MSX Expert e MSX 2
  * Desligamento ainda não implementado
    
# Licença

GNU Public License (GPL)

AVISO: Todo o conteúdo do repositório foi disponibilizado como Hobby, não visando fundos lucrativos. O uso e consequência do uso ou mau uso do conteúdo aqui apresentado é por sua conta e risco.
