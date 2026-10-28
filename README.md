# Controle de Produção — MVP

Painel web que substitui quadros físicos de controle de produção, atualizado a partir dos relatórios exportados do ERP.

> **Sobre este repositório:** o código não está publicado, porque o sistema foi feito para uso interno de uma empresa. Aqui estão a descrição do projeto e um vídeo de demonstração. **O vídeo usa dados fictícios**: nomes de clientes, unidades e referências foram inventados.

## Demonstração

https://github.com/user-attachments/assets/b3d3736f-56b4-47ed-874d-d05f5eef643d

## O problema

A empresa tem três unidades, e a gestão precisa ver o que está em produção em todas elas, separado por cliente. Hoje isso é feito em quadros físicos, com etiquetas impressas da referência do produto e datas e quantidades escritas à mão.

Todo dia, o processo é este:

1. Puxar as informações no ERP.
2. Levar os quadros de uma sala para outra.
3. Preencher à mão.
4. Levar os quadros de volta.

Os problemas desse processo:

- **Tempo:** 3 pessoas levam cerca de 1 hora para atualizar os quadros.
- **Visualização presa a uma sala:** só quem está diante do quadro consegue ver a produção.
- **Informação desatualizada:** o quadro fica defasado logo depois de preenchido.
- **Retrabalho:** os dados já existem no ERP, mas são copiados manualmente.

Eu sou uma das pessoas que preenchiam esses quadros. A ideia do MVP partiu de mim, sem que fosse uma demanda.

## A solução

Um sistema web, feito com Google Sheets e Apps Script, que lê os relatórios do ERP e monta o quadro sozinho.

**Como funciona:**

1. **Exportar:** a pessoa responsável exporta os relatórios de produção do ERP em XLSX.
2. **Enviar:** faz o upload dos arquivos no sistema. Cada arquivo é reconhecido pelo nome e associado ao seu cliente.
3. **Processar:** o sistema lê as ordens, agrupa por referência e grava tudo na planilha que funciona como base de dados.
4. **Calcular o atraso:** o atraso é calculado ordem a ordem, pela data de entrada e pelo prazo de cada cliente. Se só parte de uma referência venceu, só essa parte aparece como atraso.
5. **Exibir:** o quadro mostra unidade → cliente → referência, com totais, atraso e situação do material. Ao clicar numa referência, aparecem as ordens que a compõem.

**Regras que defini para os casos do dia a dia:**

- **Referência em arquivos diferentes:** para o cliente que envia vários arquivos, a mesma referência aparece numa linha só, sem somar duas vezes nos totais.
- **Divisão entre unidades:** uma referência pode ser dividida entre unidades. A soma das partes precisa bater com o saldo, e a divisão é desfeita quando chega um arquivo novo, para não ficar desatualizada.
- **Arquivo que não veio:** se um arquivo conhecido não vem no upload, o sistema pergunta se deve manter os dados daquele cliente ou finalizar.
- **Datas diferentes:** se os relatórios enviados têm datas diferentes, aparece um aviso indicando qual é o mais recente.
- **Tudo configurável:** clientes, prazos de atraso, arquivos e unidades são cadastrados pela tela, sem mexer no código.
- **Visualização protegida:** o quadro abre em modo de leitura, para ficar exposto numa tela ou no celular. A edição fica num modo separado.

## O resultado

- **Nos testes,** 1 pessoa atualizou o quadro em **menos de 30 minutos**, contra cerca de **1 hora com 3 pessoas** no processo atual.
- Não é preciso levar quadros de uma sala para outra: o quadro é atualizado no sistema, a qualquer hora, sempre a partir dos dados do ERP.
- A produção das três unidades, por cliente, pode ser vista de qualquer lugar, no computador ou no celular, e não só na sala dos quadros.
- **Status:** o MVP foi apresentado à direção e está em fase de testes. Ainda não substituiu os quadros físicos.
- **Próximos passos possíveis:** ligar o sistema direto ao ERP, sem exportar arquivos, e tornar configurável uma regra de agrupamento que hoje vale só para um cliente.

## Como construí

Construí o projeto com o apoio do Claude (IA da Anthropic). A divisão de papéis foi esta:

**O meu papel**
- Identificar o problema e propor a solução.
- Definir as regras de negócio: cálculo de atraso, agrupamento, divisão entre unidades e o que fazer quando um arquivo não vem.
- Desenhar as telas e o fluxo de uso.
- Testar com relatórios reais do ERP e pedir ajustes até o resultado bater com a operação. Aqui usei a minha experiência anterior em QA: casos de borda, dados incompletos e comportamento inesperado.

**O papel da IA**
- Escrever o código a partir das minhas especificações.
- Sugerir melhorias técnicas, como gravar os dados em lote e usar cache para o quadro carregar mais rápido.

O código foi escrito pela IA. As decisões sobre o que o sistema deve fazer, e a validação de que ele faz isso, foram minhas.

## Tecnologias

- **Google Sheets:** base de dados (abas para dados, ordens, clientes, unidades e cadastros)
- **Google Apps Script:** back-end e publicação como aplicativo web
- **HTML, CSS e JavaScript:** interface, que funciona no computador, na TV e no celular
- **SheetJS:** leitura dos arquivos XLSX no navegador
- **Claude (Anthropic):** apoio na escrita do código
