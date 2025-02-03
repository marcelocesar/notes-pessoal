#Processos de Autenticação

## JWT
JWT é um (padrão) mecanismo de **autenticação** baseado em token.
É usado para **transmitir informações** (identidade do usuário, permissões, etc.) de forma segura entre as partes.
Não tem dependencia de um Servidor de Autenticação centralizado ou estado de sessão.
Muito usado em **API REST**, single-page applications (SPAs) e aplicações mobile.
Pode ser usado para autenticação e autorização.

Ex: Aplicações SPA com API RESTful.

## OAuth
OAuth é um **protocolo** usado para **autorização** para aplicações web e mobile.
É usado para garantir **acesso a aplicações terceiras** à recursos em nome do usuário.
Depende de um Servidor de Autorização Centralizado.
É normalmente usado em aplicações que dependem de **API externar** ou serviços descentralizados.
É usado para **autorização**, não para autenticação.

Ex: Google e Facebook.

## SAML
SAML é um **protocolo** usado para troca de dados de autenticação e autorização entre partes, que são os identity providers (IdPs) e service providers (SPs).
É usado para estabelecer relações de confiança entre **organização** e habilitar single sign-on (**SSO**) e federação.
Depende de um identity provider (IdP).
É mais usado em **ambientes corporativos**.
Atende para ambos os cenários de **Autenticação e Autorização**.

Resumindo tudo, JWT é um mecanismo de autenticação baseado em token usado para transmitir declarações, OAuth é um protocolo usado para conceder a aplicativos de terceiros acesso a recursos em nome do usuário e SAML é um protocolo usado para troca de dados de autenticação e autorização entre as partes estabelecer relações de confiança entre as organizações.