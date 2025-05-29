<p align="center">
  <img width="700" src="https://archlinux.org/static/logos/archlinux-logo-light-1200dpi.png">
</p>

# INSTALANDO ARCH LINUX EM BIOS LEGACY


Mude layout do do teclado para ABNT2
```
loadkeys br-abnt2
```







Verificndo conecxao
```
ping 1.1.1.1
```







VERFICAR SE DATA E HORA ESTÃO SINCRONIZADOS
```
timedatectl status
```



#
### CRINDO PARTIÇÕES





USE `fdisk -l` OU `lsblk` PARA VERIFICAR AS PARTIÇÕES, E APÓS IDENTIFICAR SEU ARMAZENAMNETO USE `cfdisk`

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/0/0b/Cfdisk_interface_-_Portuguese.png">
  <p align="center"> ---> imagem inlustratva não se como base <--- </p>
</p>

>Se ouver uma pergunta do tipo de partição `gpt` ou `dos`, escolha `dos` para MBR e para UEFI escolha `gpt`.





Após entrar no cfdisk, vá em `Now` e crie uma partição de 4G e selecione `Primary`, depois vá em `Type` e use o tipo `82 Linux swap`, essa será a partição SWAP. 
A próxima partição será a `/`, então use o restante do espaço, selecione `Primary` vá em `Type` e use o `83 Linux`, spós isso selecione a patição `/` crianda e use a opção `Bootable`. 
Criado tudo pode ir em `Write` e escreva `yes` e pode sair indo em `Quit`.

|  Partições  | Tipo de ID    | Tamanho sugerido     |
| ----------- | ------------- | -------------------- |
|  `[SWAP]`   | 82 Linux swap | Pelo menos 4 GiB     |
| `/`         | 83 Linux      | Pelo menos 23-32 GiB |



#
### FORMATANDO PARTIÇÕES E ATIVANDO SWAP

Para começar vamos formatar as partições criadas para o sistema de arquivos correto, vamos começar pela partição `/`. 
Usando novamente `fdisk -l` ou `lsblk` para ver as partições.

```
#Sintax
mkfs.ext4 /dev/partição_raiz
```

```
#Exemplo
mkfs.ext4 /dev/sda2
```

Agora vamos fazer o mesmo com a memória SWAP.

```
#Sintax
mkswap /dev/partição_swap
```

```
#Exemplo
mkswap /dev/sda1
```

E vamos ativar o SWAP

```
#Sintax
swapon /dev/partição_swap
```

```
#Exemplo
swapon /dev/sda1
```



#
### MONTANDO PARTIÇÕES E INSTALANDO O SISTEMA

Para isntalar o sistema precisamos montar as partição, então vamos usar o comndo `mount`.

```
mount /dev/sda2 /mnt
```

Para instalar deve-se usar o comando `pacstrap`, e junto algusn pacotes opcionais e recomandados.

```
pacstrap /mnt base linux linux-firmware
```
O pacote base não inclui todas as ferramentas da instalação live. 
Então a instalação de mais pacotes pode ser necessário para um sistema base completamente funcional. 

> Atualizações de microcódigos de CPU — amd-ucode ou intel-ucode — para correções de bugs de hardware ou correções de segurança

> firmwares específicos para outros dispositivos não incluídos em linux-firmware (p.ex., sof-firmware para áudio onboard), linux-firmware-marvell para Marvell sem fio e qualquer um dos vários pacotes de firmware para Broadcom wireless)

> softwares necessários para rede (p.ex., networkmanager).

> um editor de texto de console

```
genfstab -pU /mnt >> /mnt/etc/fstab
```



#
### INSTALANDO O GRUB

Use o `pacstrap` para instalar o grub.

```
pacstrap /mnt grub
```



#
### CONFIGURANDO O SISTEMA

É preciso sair do sistema live para configurar, então se usa o `arch-chroot`.
E assim pode preparar o sistema.

```
arch-chroot /mnt
```

Em `usr/share/zoneinfo` pode ver todas as regiões, procure sua região para sincronizar o relogio.
```
ln -sf usr/share/zoneinfo/sua localização /etc/localtime

hwclock --systohc
```

Usando o `echo`, pode-se crir o hostname (pode usar um editor de texto no lugar do `echo`.

```
echo nome_da_maquina > /etc/hostname
```

Para o idioma use `locale-gen`, será gerado um arquivo em `/etc/locale.gen`, e descomente os idiomas de sua escolha. Digite novamente `locale-gen`.

O layout do teclado editando usando (`echo` ou um editor de texto) o aquivo `/etc/vconsole.conf`.

```
KEYMAP=br-abnt2
```

Para finalizar o boot com o hrub use o `grub-install`, e crie o arquivo grubconfig.

```
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

É recomendando colocar uma senha no usuário root.

```
passwd
```
