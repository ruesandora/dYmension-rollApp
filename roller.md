<h1 align="center"> Roller Kurulumu </h1>

> roller için node veya validatör kurmanıza gerek yok.

```console
curl -L https://dymensionxyz.github.io/roller/install.sh | bash
```console
roller version
```

> Komutu girdikten sonra:

```console
roller config init --interactive
```

> 1- İsim verin mesela, `myruesrollapp_6419-2` gibi.

> 2- Token ismi belirleyin

> 3- Supply belirleyin

> 4- Roller tipi seçin (size kalmıs)

> 5- Zincir seçin (size kalmıs)

> Sonra size verilen çıktıda `3 adres` olacak `2 tanesi dym` adresi `1 tanesi avail veya celestia` olacak, bunlar içinde `faucetten` token alın.
```console
# Bu komuttan sonra has been successfully  çıktısı alacaksınız.
roller register
```
```console
# Çalıştıralım
apt install screen
screen -S roller
roller run
```

> Sonra karşınıza bir ekran çıkacak ve `channel-id` arayacaksınız.
> Erkenciyseniz ve benim gibi şanslıysanız `channel-idinizi bulursunuz`, bulamazsanız sıkıntı. `(bundan dolayı ilk burdan başlayın dedim)`

![image](https://github.com/ruesandora/dYmension-rollApp/assets/101149671/d04bd72a-f73f-40f8-bb6b-0ac834b90610)


> DURUMA GÖRE DEVAMI OLURSA GELECEK.
