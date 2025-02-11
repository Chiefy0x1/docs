---
title: Résolution des erreurs TLS
intro: 'Si vous rencontrez des problèmes TLS avec votre appliance, vous pouvez prendre des mesures pour les résoudre.'
redirect_from:
  - /enterprise/admin/articles/troubleshooting-ssl-errors
  - /enterprise/admin/categories/dns-ssl-and-subdomain-configuration
  - /enterprise/admin/installation/troubleshooting-ssl-errors
  - /enterprise/admin/configuration/troubleshooting-ssl-errors
  - /admin/configuration/troubleshooting-ssl-errors
  - /admin/configuration/configuring-your-enterprise/troubleshooting-ssl-errors
versions:
  ghes: '*'
type: how_to
topics:
  - Enterprise
  - Errors
  - Infrastructure
  - Networking
  - Security
  - Troubleshooting
shortTitle: Troubleshoot TLS errors
ms.openlocfilehash: 855737f89f0380333b1f37c26d512c889f2ee786
ms.sourcegitcommit: fb047f9450b41b24afc43d9512a5db2a2b750a2a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2022
ms.locfileid: '147881250'
---
## Suppression de la phrase secrète de votre fichier de clé

Si vous disposez d’une machine Linux sur laquelle OpenSSL est installé, vous pouvez supprimer votre phrase secrète.

1. Renommez votre fichier de clé d’origine.
  ```shell
  $ mv yourdomain.key yourdomain.key.orig
  ```
2. Générez une nouvelle clé sans phrase secrète.
  ```shell
  $ openssl rsa -in yourdomain.key.orig -out yourdomain.key
  ```

Vous êtes alors invité à entrer la phrase secrète de la clé au moment d’exécuter cette commande.

Pour plus d’informations sur OpenSSL, consultez la [documentation d’OpenSSL](https://www.openssl.org/docs/).

## Conversion de votre certificat ou clé TLS au format PEM

Si vous avez installé OpenSSL, vous pouvez convertir votre clé au format PEM à l’aide de la commande `openssl`. Par exemple, vous pouvez convertir une clé du format DER au format PEM.

```shell
$ openssl rsa -in yourdomain.der -inform DER -out yourdomain.key -outform PEM
```

Sinon, vous pouvez utiliser l’outil SSL Converter pour convertir votre certificat au format PEM. Pour plus d’informations, consultez la [documentation de l’outil SSL Converter](https://www.sslshopper.com/ssl-converter.html).

## Installation sans réponse après le chargement d’une clé

Si {% data variables.product.product_location %} ne répond pas après avoir chargé une clé TLS, [contactez le support {% data variables.product.prodname_enterprise %}](https://enterprise.github.com/support) avec des détails précis, notamment une copie de votre certificat TLS. Vérifiez que votre clé privée **n’est pas** incluse. 

## Erreurs de validité de certificat

Certains clients comme les navigateurs web et Git en ligne de commande affichent un message d’erreur s’ils ne peuvent pas vérifier la validité d’un certificat TLS. Cela se produit souvent avec les certificats auto-signés ainsi que les certificats « racines chaînés » émis à partir d’un certificat racine intermédiaire qui n’est pas reconnu par le client.

Si vous utilisez un certificat signé par une autorité de certification, le fichier de certificat que vous chargez sur {% data variables.product.prodname_ghe_server %} doit inclure une chaîne de certificats avec le certificat racine de cette autorité de certification. Pour créer un tel fichier, concaténez l’intégralité de votre chaîne de certificats (ou « bundle de certificats ») à la fin de votre certificat, en veillant à ce que le certificat principal avec votre nom d’hôte arrive en premier. Sur la plupart des systèmes, vous pouvez faire cela avec une commande de ce type :

```shell
$ cat yourdomain.com.crt bundle-certificates.crt > yourdomain.combined.crt
```

Vous devriez pouvoir télécharger un bundle de certificats (par exemple, `bundle-certificates.crt`) à partir de votre autorité de certification ou fournisseur TLS.

## Installation de certificats racines auto-signés ou d’une autorité de certification non approuvé

Si votre appliance {% data variables.product.prodname_ghe_server %} interagit avec d’autres machines sur votre réseau et qu’elles utilisent un certificat auto-signé ou non approuvé, vous devez importer le certificat racine de l’autorité de certification de signature dans le magasin de certificats à l’échelle du système afin d’accéder à ces systèmes via HTTPS.

1. Obtenez le certificat racine de l’autorité de certification auprès de votre autorité de certification locale et vérifiez qu’elle est au format PEM.
2. Copiez le fichier dans votre appliance {% data variables.product.prodname_ghe_server %} via SSH en tant qu’utilisateur « administrateur » sur le port 122.
  ```shell
  $ scp -P 122 rootCA.crt admin@HOSTNAME:/home/admin
  ```
3. Connectez-vous à l’interpréteur de commandes d’administration de {% data variables.product.prodname_ghe_server %} via SSH en tant qu’utilisateur « administrateur » sur le port 122.
  ```shell
  $ ssh -p 122 admin@HOSTNAME
  ```
4. Importez le certificat dans le magasin de certificats à l’échelle du système.
  ```shell
  $ ghe-ssl-ca-certificate-install -c rootCA.crt
  ```

## Mise à jour d’un certificat TLS

Vous pouvez générer un nouveau certificat auto-signé ou mettre à jour un certificat TLS existant pour {% data variables.product.product_location %} avec l’utilitaire en ligne de commande `ghe-ssl-certificate-setup`. Pour plus d’informations, consultez « [Utilitaires en ligne de commande](/admin/configuration/configuring-your-enterprise/command-line-utilities#ghe-ssl-ca-certificate-setup) ».
