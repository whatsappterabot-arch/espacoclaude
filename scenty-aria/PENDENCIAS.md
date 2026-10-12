# Scenty Store / ArIA: estado e pendências

Atualizado em 12/10/2026.

## O que já funciona

| Peça | Onde | Situação |
|---|---|---|
| Sincronização diária de produtos (19h, horário de Brasília) | n8n: "SYNC - Produtos Nuvemshop (diario 19h)" | Ativo, credencial v2 |
| Atualização em tempo real (webhooks de produto e pedido) | n8n: "WEBHOOK - Nuvemshop eventos (tempo real)" | Ativo, credencial v2, testado em 09/10 |
| Busca de produtos da ArIA (devolve variacao_id, nunca estoque) | Agente "SCH Chat - Agente IA", ferramenta consultar_produtos | Ativo |
| Quiz de fragrâncias (matriz completa, 2.205 combinações) | Ferramenta consultar_quiz_fragrancias | Ativo |
| Criação de pedido com link de pagamento | Ferramenta criar_pedido + n8n: "TOOL - Criar pedido Nuvemshop (ArIA)" | Ativo, testado pelo WhatsApp em 10/10 |
| Credencial Nuvemshop | "Nuvemshop API v2 (Scenty 8329094, com pedidos manuais)" | Escopos: produtos (leitura), pedidos e pedidos manuais |

## Pendências

### Prompt principal da ArIA (vem do banco, via SCH Chat)
- [ ] Remover a promessa de "5% de desconto no Pix". No teste de 10/10 a ArIA disse ao cliente "Pix (costuma ter 5% de desconto)". Se a loja não dá esse desconto, é promessa falsa no momento do pagamento.
- [ ] Trocar links e domínio antigos (scentystore.com) pelo endereço atual da loja.
- [ ] Atualizar categorias e lista de fragrâncias para as da Nuvemshop.
- [ ] Ajustar a seção do quiz: o prompt ainda manda consultar a ferramenta a cada resposta; a regra atual é consultar só depois das 5.
- [ ] Ajustar o "Fechamento": hoje a ArIA cria o pedido e manda o link de pagamento.

### Loja (depende da Eliana)
- [ ] Trocar a titularidade do Nuvem Pago de CPF para CNPJ e cadastrar a conta corrente da empresa (mensagem para o suporte já redigida; envio pelo botão "?" > "Fale conosco" no painel).
- [ ] Conferir peso e medidas de todos os produtos (necessário para o cálculo de frete).
- [ ] Ativar controle de estoque nos produtos que precisam.
- [ ] Responder as dúvidas do quiz (Sea Blue = Sea Azul; Flor de Laranjeira e Wood Herbs; 5 linhas padronizadas; refil faltante). Se algo mudar, reimportar com "TEMP - Importar quiz (Claude)".
- [ ] Pagar o plano da loja (aviso "Pague o plano em 1 dia" visto em 09/10).

### Técnico
- [x] (12/10) A ArIA reenviava da memória um link de pagamento antigo, que dava 404 quando o pedido tinha sido apagado. Corrigido: ela não reenvia link antigo e gera um pedido novo, após confirmar o resumo.
- [x] (12/10) Erro de formato intermitente ("Desculpe, tive um probleminha..."). Causa: com a opção "Use Responses API" ligada no nó OpenAI Chat Model1, o GPT-5 às vezes devolvia a resposta embrulhada em blocos ([{"type":"text","text":"..."}]) e o leitor de formato rejeitava como JSON inválido. A resposta em si estava certa. Correção: opção desligada (responsesApiEnabled = false). Depois disso, 3 de 3 repetições do caso que falhava passaram.
- [ ] Acompanhar o erro de formato por alguns dias. Consulta no banco do n8n (schema awkwardgiantpanda): contar execuções do agente FJPVhGCmCI6km0pi cujo execution_data contém "fit required format".
- [ ] Pedidos rascunho abandonados se acumulam no painel cada vez que a ArIA gera um link novo. Decidir se apagamos automaticamente os não pagos depois de alguns dias.
- [ ] Identificador de cliente fixo (id_cliente = 2) nas consultas do agente.
- [ ] Workflow para as 3 URLs de LGPD da Nuvemshop (apenas receber e registrar).
- [ ] Gerar nova chave secreta (client secret) do app Integra_n8n no portal de parceiros; a atual apareceu num print. Depois, refazer a troca de token.
- [ ] Limpar da memória da ArIA as conversas de teste do número 5511981880079.
- [ ] Bateria de testes de conversa (quiz completo até o pedido, item indisponível, cliente que muda de ideia, pedido com vários itens).
- [ ] Apagar workflows temporários quando não forem mais úteis: "TEMP - Teste pedido rascunho (Claude)", "TEMP - Apagar rascunhos de teste (Claude)", "TEMP - Conferencias (Claude)", "TEMP - Importar quiz (Claude)". Perguntar antes.
- [ ] Apagar a credencial antiga "Nuvemshop API (Scenty Store 8329094)", que não é mais usada.
- [ ] Decidir o destino da tabela antiga do quiz. Perguntar antes de apagar.
