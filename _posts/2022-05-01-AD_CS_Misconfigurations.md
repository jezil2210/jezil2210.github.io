---
title: AD CS Misconfigurations [PT-BR]
date: 2022-05-01 15:00:00 +/-TTTT
categories: [Active Directory, Certificate Services (AD CS)]
tags: [AD, AD CS, Certificates, Kerberos]     

comments: false
img_path: /assets/img/posts/ad-cs/

---

## Introdução

AD CS é a implementação de uma infraestrutura de chave pública (PKI) da Microsoft, que inúmeras organizações utilizam para várias necessidades de segurança, como proteger servidores da web (SSL), autenticação baseada em certificado, assinaturas digitais para documentos e criptografar e-mails. 

Dessa forma, o foco da maior parte das vulnerabilidades encontradas nesse serviço é a funcionalidade “autenticação baseada em certificado”, porém, antes de entrar em detalhes é necessário que alguns conceitos estejam brevemente esclarecidos, sem serem definidos de forma complexa.

- PKI (Public Key Infrastructure) — Sistema responsável por gerenciar certificados.
- AD CS (Active Directory Certificate Services) — Implementação de uma infraestrutura de chave pública (PKI) da Microsoft.
- CA (Certificate Authority) — Servidor PKI que fornece certificados. 
- Enterprise CA — CA integrado com o AD (o oposto do standalone CA); ele que fornece certificados no AD.
- Certificate Template — Uma coleção de configurações e políticas que definem o conteúdo de um certificado fornecido por um enterprise CA. Em resumo, com informações como: “Por quanto tempo o certificado é válido?”, “Para que esse certificado é usado?”, “Quem é o dono desse certificado?”, “Quem pode pedir esse certificado?”
- CSR (Certificate Signing Request) — A mensagem enviada ao CA para pedir um certificado. 
- EKU (Extended/Enhanced Key Usage) — Um ou mais identificadores de objetos que definem como um certificado pode ser utilizado.

## AD CS – Comunicação cliente e CA

Agora que os conceitos básicos foram apresentados, a seguir será demonstrado como funciona a comunicação entre um cliente – Usuário ou Computador – que deseje realizar o pedido de um certificado ao enterprise CA.

![img-description](fluxo-certificados.jpeg)
_Fluxo de comunicação entre um cliente e CA_

1.	Inicialmente, o usuário que deseja pedir um certificado ao CA necessita criar um par de chave pública/privada para poder incluir ela no CSR (Mensagem para pedir o certificado).
2.	Em seguida, ele gera o CSR com o template do certificado que ele deseja, com o subject – o dono desse certificado –, os EKUs, e a chave pública que ele gerou na etapa anterior, e envia o CSR para o CA.
3.	O CA então verifica se o usuário pode pedir certificados, e se for permitido, ele vai gerar o certificado baseando no template que foi especificado no CSR. O template contêm todos os detalhes importantes, inclusive se o cliente possui permissão para pedir esse certificado em especifico.
4.	Caso ele possua essa permissão, o CA irá gerar um certificado baseado no template especificado no CSR, além de assinar ele com sua chave privada.
5.	O certificado é enviado para o usuário.
6.	E por fim, o usuário armazena esse certificado para utilizar ele baseando-se nas permissões que ele é permitido.

