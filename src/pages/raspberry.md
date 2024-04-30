# Raspberry Pi

### Como criar um botao de shutdown e start mais simples :)

adicionar:
`dtoverlay=gpio-shutdown`
em:
`/boot/config.txt`

Ligar um botão entre os pinos 5 e 6 (GPIO3 e GND)

E já está. Não podia ser mais facil.

A documentação sobre isto está aqui:  
https://github.com/raspberrypi/firmware/blob/master/boot/overlays/README