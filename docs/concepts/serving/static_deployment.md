# Modelo como Parte da Aplicação (Static Deployment)

## Introdução

Nesta abordagem $-$ que Andriy Burkov chama de Static Deployment $-$ o modelo é empacotado como parte da aplicação que então é instalada através de um arquivo de distribuição de aplicações, como, por exemplo: arquivo binário executável, JAR, APK, DEB, etc.

## Vantagens x Desvantagens

Static deployment é uma estratégia adequada para modelos simples que precisam estar disponíveis o tempo todo (mesmo se o usuário estiver offline). Também é adequado para inferências em batches ou fluxo. Porém, o retreinamento tende estar restrito a lotes, uma vez que é necessário atualizar toda a aplicação para incluir uma nova versão do modelo.

### Vantagens

- Não é preciso enviar dados do usuário para um servidor (ou qualquer recurso) externo ao dispositivo do usuário
- O modelo estará sempre disponível, mesmo se o usuário estiver offline (sem conexão com a Internet)
- Caso seja um modelo simples, sem necessidade de computações rápidas ou pesadas, o tempo de inferência é muito mais rápido na abordagem "estático" quando comparado com qualquer outra estratégia

### Desvantagens

- Para atualizar o modelo é necessário atualizar toda a aplicação (ou seja, reconstruir o arquivo de distribuição da aplicação mesmo que apenas o modelo tenha sofrido alterações)
- Executar o monitoramento de performance do modelo é extremamente difícil
- Se a computação do modelo for cara, executá-la no dispositivo do usuário pode ser ineficiente ou prejudicar a experiência do usuário