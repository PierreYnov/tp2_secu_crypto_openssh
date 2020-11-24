# TP2 : Chiffrement et authentification à l'aide du chiffrement asymétrique
## Sécurisation des sessions distantes avec openssh

## Classe : B3B
## Elèves : Emma Durand **[@emmadrd912](https://github.com/emmadrd912)** et Pierre Ceberio **[@PierreYnov](https://github.com/PierreYnov)** 

![](https://s1.o7planning.com/fr/11409/images/7450359.jpeg)

# Sommaire 


Lab : serveur Linux avec 2 clients :
- 1 Linux via le client SSH
- 1 Windows via l'utilitaire putty


## 3.1 Rappels théoriques

**Rappelez le fonctionnement du chiffremennt asymétrique**

La cryptographie asymétrique est un procédé qui intègre deux clés de chiffrement, une clé publique et une clé privée. La clé publique du destinataire est utilisée pour chiffrer et la clef privée du destinataire pour déchiffrer un message. Le même algorithme est utilisé pour les deux clefs.

**Comment est assuré l'authentification ?**

 puisqu’une clé privée unique a été utilisée pour la signature du message ou du document, son destinataire a la garantie que l'identité du signataire est légitime.



## 3.2 Préparation du serveur :



### 3.2.1 Conseils 

mode verbeux ssh  = ssh -v

voir les man

### 3.2.2 Première connexion :

- démarrage de ma ubuntu

- création compte (ici pierre)

- éditez fichier /etc/ssh/sshd_config pour interdire connexion compte root

Passage de PermitRootLogin en no puis restart du service sshd

Vérifier que seulement protocole SSHv2 est accepté 

Ajout de la ligne Protocol 2


**Consultez les recommandations de l'ANSSI, quelles mesures supplémentaires pourriez vous
prendre pour renforcer la configuration de votre serveur OpenSSH ?**

- SSH doit être utilisé en lieu et place de protocoles historiques (TELNET, RSH, RLOGIN)
pour des accès shell distants.
- Les serveurs d’accès distants TELNET, RSH, RLOGIN doivent être desinstallés du
système.
- SCP ou SFTP doivent être utilisés en lieu et place de protocoles historiques (RCP, FTP)
pour du transfert ou du téléchargement de fichier
- La mise en place de tunnels SSH doit être strictement réservée aux protocoles n’offrant
pas de mécanismes de sécurité robustes et pouvant en tirer un bénéfice relatif (par
exemple : X11, VNC).
Cette recommandation n’exclut pas l’usage de protocoles de sécurité plus bas niveau
supplémentaires comme IPSEC (cf. "Recommandations de sécurité relatives à IPsec" sur
www.ssi.gouv.fr).
- Il faut s’assurer de la légitimité du serveur contacté avant de poursuivre l’accès. Cela
passe par l’authentification préalable de la machine au travers de l’empreinte de sa clé
publique, ou d’un certificat valide et vérifié.
- L’usage de clés DSA n’est pas recommandé.
- La taille de clé minimale doit être de 2048 bits pour RSA.
- La taille de clé minimale doit être de 256 bits pour ECDSA.
- Lorsque les clients et les serveurs SSH supportent ECDSA, son usage doit être préféré à
RSA.
- Les clés doivent être générées dans un contexte où la source d’aléa est fiable, ou à défaut
dans un environnement où suffisamment d’entropie a été accumulée
- Quelques règles permettent de s’assurer que le réservoir d’entropie est correctement
rempli :
• la machine de génération de clés doit être une machine physique ;
• elle doit disposer de plusieurs sources d’entropie indépendantes ;
• l’aléa ne doit être obtenu qu’après une période d’activité suffisamment importante
(plusieurs minutes voire heures).
- La clé privée ne doit être connue que de l’entité qui cherche à prouver son identité à un
tiers et éventuellement d’une autorité de confiance. Cette clé privée doit être dûment
protégée pour en éviter la diffusion à une personne non autorisée.
- L’AES-128 mode CBC doit être utilisé comme algorithme de chiffrement pour la
protection de la clé privée utilisateur par mot de passe.

et au total 31 recommandations

Avec OpenSSH, ce contrôle se fait de plusieurs façons :
• en s’assurant que l’empreinte de la clé présentée par le serveur est la bonne (obtenue
préalablement avec ssh-keygen -l) ;
• en rajoutant la clé manuellement dans le fichier known_hosts ;
• en vérifiant la signature du certificat présenté par le serveur avec une autorité de
certification (AC) reconnue par le client.


**faites un ssh-keygen**

je la copie colle

### 3.2.3 Seconde connexion :

je me connecte avec ssh pierre@192.168.1.24 et répond no

    The authenticity of host '192.168.1.24 (192.168.1.24)' can't be established.
    ECDSA key fingerprint is SHA256:NXOg+pjbszJxGpTKNS2iMqBph2GaJA1nzsQhpriEWXc.
    Are you sure you want to continue connecting (yes/no)? no
    Host key verification failed.

**Expliquez pourquoi le système vous pose cette question**

OpenSSH adopte par défaut un modèle de sécurité Trust On First Use (TOFU) : lors de la première
connexion et à défaut de pouvoir authentifier l’hôte, ssh demande confirmation à l’utilisateur qu’il
s’agit bien de la bonne clé (via son empreinte). Si l’utilisateur confirme que l’empreinte est bonne, ssh
procèdera à son enregistrement dans le fichier known_hosts afin de permettre sa vérification lors des
visites suivantes.

**Vérifier le fingerprint**

- Sur le serveur, pour obtenir le fingerprint : 

    ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub

On remarque que sur notre client, pendant la demande de connexion, on obtiens le même fingerprint.

**Que va t-il se passer si vous acceptez la clef publique ?**

Le fichier known_hosts se met à jour avec l'ajout de la clef afin de permettre sa vérification lors des prochaines visites.

Je vérifie en allant voir le fichier et je vois que une nouvelle ligne a été ajouté : 

![](https://i.gyazo.com/1b84ded83061dea32af11d4d9c5d88ea.png)

## 3.3 Préparation du client UNIX/BSD :



## 3.4 Préparation du client Windows :