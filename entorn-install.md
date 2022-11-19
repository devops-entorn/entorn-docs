# ENTORN v2223 Documentació

**Autor: Oriol Boix Anfosso** ([github](https://github.com/orboan))

## Instal·lació de l'Entorn sobre un sistema Windows

**Prerequisits**: 

- 8 Gb RAM mínim, 16Gb recomanats.
- 100 Gb SSD mínim, 150 Gb recomanats (espai lliure necessari).
- Aquesta implementació de l'Entorn v0.1.0 (v2223) és compatible només amb processadors Intel / AMD x86_64.
- Windows 10 versió > 1903, Windows 11, Windows Server 2019 en endavant.
- Windowns Subsystem for Linux, versió 2.
- Ubuntu per a WSL.
- Docker Desktop amb Kubernetes / Rancher Desktop.
- Helm.


### Instal·lació de WSL v2

A un terminal PowerShell **com a Administrador**, executem:

```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

**Ara reiniciem l'ordinador**.

A continuació, descarreguem i instal·lem el següent paquet:

[Darrera versió del Nucli de Linux per a WSL 2](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)


**Establim la versió 2 de WSL com a versió per defecte**: obrim PowerShell com a administrador, i executem:

```
wsl --set-default-version 2
```


### Instal·lació d'Ubuntu per a WSL

Obrim Microsoft Store i cerquem _Ubuntu_.

Descarreguem i instal·lem Ubuntu per a WSL.

Un cop instal·lat des del Microsoft Store, cal arrencar l’Ubuntu (clicar a Launch) perquè s’acabi d’instal·lar: **Haureu de crear un usuari per a Ubuntu**. 

_Nota: recordeu la contrasenya que establiu per a l'usuari, la necessitareu._


**Recomanable**: Executem la següent comanda a un PowerShell com a administrador:

```
wsl --set-version Ubuntu 2
```

Si la versió d'Ubuntu que s'ha instal·lat és la 22.04 (per exemple), executaríem:

```
wsl --set-version Ubuntu-22.04 2
```

Per a saber quines versions d'Ubuntu (o d'altres) tenim instal·lades a WSL, a PowerShell podem escriure:

```
wsl --list
```

### Com limitar el consum de memòria per part de WSL

Lús de memòria per part de WSL és, per defecte, el següent:

> _50% of total memory on Windows or 8GB, whichever is less; on builds before 20175: 80% of your total memory on Windows_

Per tant, consumeix molta memòria! Però es pot limitar, seguint les següents instruccions que trobareu [aquí](https://www.aleksandrhovhannisyan.com/blog/limiting-memory-usage-in-wsl-2/) o [aquí](https://learn.microsoft.com/en-us/windows/wsl/wsl-config#configure-global-options-with-wslconfig).

**Atenció**: Per a executar l'Entorn, Docker Desktop necessitarà almenys 6Gb de memòria RAM, per tant, no limiteu la memòria de WSL per sota d'aquest valor.


### Instal·lació de Docker Desktop

[Descarreguem i instal·lem](https://docs.docker.com/desktop/install/windows-install/) la darrera versió de Doker Desktop per a Windows.

Un cop instal·lat Docker Desktop, cal ajustar-ne la configuració:


_General_:

- Start Docker Desktop when you log in:  activat només si tens >= 16 Gb RAM.
- Expose daemon ... without TLS: **activat**
- Use the WSL 2 based engine: **activat**
- Use Docker Compose v2: **activat**
- la resta d'opcions, **desactivades**


_Resources > WSL integration_:

- Enable integration with my default WSL 2 distro: **activat**
- Enable additional distros > Ubuntu: **activat**


_Docker Engine_:

- experimental: canviar de _false_ a _true_


_Kubernetes_:

- Enable Kubernetes: **activat**


_Software updates_:

- **Ho desactivem tot**


Finalment, cliquem al botó "**Apply and Restart**".


### Instal·lació de Helm

Helm és un gestor de paquets de Kubernetes. Des del terminal del bash d’Ubuntu, l’instal·larem:

```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null

sudo apt-get install apt-transport-https --yes

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list

sudo apt-get update
sudo apt-get install helm

```


### Instal·lació de l'Entorn

Cal instal·lar l'entorn en un disc que tingui com més espai lliure millor (mínim 25 a 30 Gb, tot i que amb 20 Gb ja podria funcionar).

En aquest exemple, estem suposant que la unitat D: (sistema Windows) és la unitat que té més espai lliure. Si no tens una unitat D:, escull una altra.

Obrim un PowerShell com a Administrador, i executem:

```
cd d:\
mkdir entorn
attrib +h entorn
```

Obrim un terminal d'Ubuntu i executem:

```
cd /mnt/d/entorn
git clone https://github.com/entorn-io/klocal-ddesk-jbe-min.git
cd klocal-ddesk-jbe-min
./entorn-pull.sh
```

**Atenció**: quan executeu ``./entorn-pull.sh`` es descarregueran de dockerhub 3 imatges de docker. En finalitzar aquest script se us mostraran les imatges descarregades. Si no en són 3 (menys de 3) cal executar de nou aquest script abans d'executar el següent script ``./entorn-install.sh``. L'script ja us informarà si cal tornar-lo a executar o no.

> Per a saber quines i quantes imatges de docker que contenen al nom **entorn** tenim descarregades, podem executar la següent comanda: ``docker images | grep entorn``. N'hem de tenir 3 (en cas contrari, cal executar de nou ``./entorn-pull.sh``)

A continuació, un cop descarregades totes cinc imatges de docker, cal executar (en el mateix terminal d'Ubuntu):

```
cd /mnt/d/entorn/klocal-ddesk-jbe-min
./entorn-install.sh
./entorn-start.sh
```

### Actualització de l'Entorn

Si voleu actualitzar l'entorn, cal tornar a clonar el repositori amb ``git clone`` (perquè pugui clonar caldrà eliminar primer el directori de la versió antiga), i un cop clonat, executar de nou els scripts:

```
cd /mnt/d/entorn
rm -rf klocal-ddesk-jbe-min
git clone https://github.com/entorn-io/klocal-ddesk-jbe-min.git
cd klocal-ddesk-jbe-min
./entorn-pull.sh
./entorn-install.sh
./entorn-start.sh
```


## Troubleshooting

### Consum excesiu de memòria per part de Docker Desktop

Cal limitar la memòria consumida per WSL2, que és el responsable de l'ús de la memòria. A munt, en un apartat anterior d'aquest document, ho teniu explicat.

### El meu Windows es queda congelat (es penja) durant el pull de les imatges de docker (en executar ./entorn-pull.sh)

En aquest cas, canvieu la següent configuració de Docker Desktop:

Docker Engine > afegiu ``"max-concurrent-downloads": 1``. Us quedaria així:

```
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": true,
  "max-concurrent-downloads": 1,
  "features": {
    "buildkit": true
  }
}
```

Un cop modificada la configuració, no oblideu d'aplicar els canvis (_Apply & Restart_)

### Quan arrenco l'entorn, la barra de progrés no avança

Això passa perquè l'anterior vegada que es va arrencar l'entorn, no es va tancar correctament.

> Per a tancar l'entorn correctament, abans de tancar el navegador, a la web del JupyterLab heu d'anar al menú a ``File -> Hub Control Panel`` i un cop al Hub, clicar a "Stop My Server".

Per a poder solucionar aquest problema, cal executar en un terminal bash d'Ubuntu la següent comanda, que reseteja l'entorn i ja el podreu tornar a arrencar amb normalitat:

```
entorn reset
``` 


