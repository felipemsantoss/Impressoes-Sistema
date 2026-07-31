# Documentação de Mapeamento de BI — Global_ReportTest

Este documento detalha o funcionamento e os mapeamentos de dados implementados na classe `Global_ReportTest`. O componente é responsável por gerenciar a renderização dinâmica de layouts de relatórios em PDF, utilizando consultas estruturadas em views de Business Intelligence (BI) e aplicando regras de fallback para tabelas físicas quando necessário.

---

## 📌 Visão Geral do Sistema

O motor de renderização adota uma abordagem híbrida para a busca de dados:
1. **Views de BI (`bi_*`)**: São priorizadas por consolidarem regras de negócio e dados tratados.
2. **Fallback Físico**: Se a view de BI não retornar dados para o laço correspondente, o sistema executa automaticamente uma consulta secundária na tabela física legada.
3. **Polimorfismo Dinâmico**: Identifica em tempo de execução o tipo de operação (como Entrada/Saída para Notas Fiscais e Pedidos) para redirecionar as consultas para as fontes corretas.

---

## 🗂️ Dicionário de Mapeamentos Estáticos

A tabela abaixo lista os módulos estáticos mapeados no método `getSchemaMapping`:

| Módulo Lógico | View/Tabela Master | Chave do Loop | View/Tabela do Loop | Fallback Físico | Chave Estrangeira (FK) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Atendimento Clínico** | `bi_clinic_atendimento` | `servicos` | `bi_clinic_atendimento_servico` | `clinic_atendimento_servico` | `clinic_atendimento_id` |
| **Compras Requisitos** | `bi_compra_requisito` | `produtos` | `bi_compra_requisito_produto` | `compra_requisito_produto` | `compra_requisito_id` |
| **Consórcio** | `bi_consorcio` | *(Nenhum)* | — | — | — |
| **CRM Briefing** | `bi_crm_briefing` | *(Nenhum)* | — | — | — |
| **Currículo Profissional** | `bi_curriculo` | `cursos`<br>`experiencias`<br>`idiomas`<br>`profissoes`<br>`vagas_emprego` | `bi_curriculo_curso`<br>`bi_curriculo_experiencia`<br>`bi_curriculo_idioma`<br>`bi_curriculo_profissao`<br>`bi_curriculo_vaga_emprego` | `curriculo_curso`<br>`curriculo_experiencia`<br>`curriculo_idioma`<br>`curriculo_profissao`<br>`curriculo_vaga_emprego` | `curriculo_id` |
| **Balanço de Estoque** | `bi_estoque_balanco` | `produtos` | `bi_estoque_balanco_produto` | `estoque_balanco_produto` | `estoque_balanco_id` |
| **Comissão de Faturamento** | `bi_fatur_comis` | `gerados` | `bi_fatur_comis_gerado` | `fatur_comis_gerado` | `fatur_comis_id` |
| **Contratos** | `bi_fatur_contrato` | `animais`<br>`parcelas`<br>`imoveis`<br>`pessoas`<br>`produtos`<br>`servicos`<br>`servicos_plano`<br>`veiculos` | `bi_fatur_contrato_clinic_animal`<br>`bi_fatur_contrato_financ_titulo`<br>`bi_fatur_contrato_imovel`<br>`bi_fatur_contrato_pessoa`<br>`bi_fatur_contrato_produto`<br>`bi_fatur_contrato_servico`<br>`bi_fatur_contrato_servico_plano`<br>`bi_fatur_contrato_veiculo` | `fatur_contrato_clinic_animal`<br>`fatur_contrato_financ_titulo`<br>`fatur_contrato_imovel`<br>`fatur_contrato_pessoa`<br>`fatur_contrato_produto`<br>`fatur_contrato_servico`<br>`fatur_contrato_servico_plano`<br>`fatur_contrato_veiculo` | `fatur_contrato_id` |
| **Desconto de Títulos** | `bi_financ_desc_titulo` | `titulos` | `bi_financ_desc_titulo_financ_titulo` | `financ_desc_titulo_financ_titulo` | `financ_desc_titulo_id` |
| **Inadimplência** | `bi_financ_inad` | *(Nenhum)* | — | — | — |
| **Recibos** | `bi_financ_recibo` | *(Nenhum)* | — | — | — |
| **PCP 2 OP** | `bi_pcp2_op` | `produtos`<br>`materiais`<br>`processos` | `bi_pcp2_op_produto`<br>`bi_pcp2_op_produto_lista_mat`<br>`bi_pcp2_op_produto_processo` | `pcp2_op_produto`<br>`pcp2_op_produto_lista_mat`<br>`pcp2_op_produto_processo` | `pcp2_op_id` |
| **PCP Apontamentos** | `bi_pcp_apontamento` | *(Nenhum)* | — | — | — |
| **PCP Ficha** | `bi_pcp_ficha` | `equipamentos`<br>`especificacoes`<br>`processos`<br>`produtos`<br>`testes_qualidade` | `bi_pcp_ficha_equipamento`<br>`bi_pcp_ficha_especificacao`<br>`bi_pcp_ficha_processo`<br>`bi_pcp_ficha_produto`<br>`bi_pcp_ficha_teste_qld` | `pcp_ficha_equipamento`<br>`pcp_ficha_especificacao`<br>`pcp_ficha_processo`<br>`pcp_ficha_produto`<br>`pcp_ficha_teste_qld` | `pcp_ficha_id` |
| **Produção 1 (PCP OP)** | `bi_pcp_op` | `equipamentos`<br>`processos`<br>`produtos`<br>`materiais`<br>`testes_qualidade` | `bi_pcp_op_equipamento`<br>`bi_pcp_op_processo`<br>`bi_pcp_op_produto`<br>`bi_pcp_op_produto`<br>`bi_pcp_op_teste_qld` | `pcp_op_equipamento`<br>`pcp_op_processo`<br>`pcp_op_produto`<br>`pcp_op_produto`<br>`pcp_op_teste_qld` | `pcp_op_id` |
| **Produção 2 (PCP 2)** | `bi_pcp2_op` | `produtos`<br>`materiais`<br>`processos` | `bi_pcp2_op_produto`<br>`bi_pcp2_op_produto_lista_mat`<br>`bi_pcp2_op_produto_processo` | — | `pcp2_op_id` |
| **Pessoas** | `bi_pessoa` | `analises_pf`<br>`enderecos`<br>`atividades` | `bi_pessoa_analise_pf`<br>`bi_pessoa_endereco`<br>`bi_pessoa_tributo_tab_atividade` | `pessoa_analise_pf`<br>`pessoa_endereco`<br>`pessoa_tributo_tab_atividade` | `pessoa_id` |
| **Pessoa Análise PF** | `bi_pessoa_analise_pf` | `emails`<br>`enderecos`<br>`fones`<br>`negativos`<br>`participacoes` | `bi_pessoa_analise_pf_email`<br>`bi_pessoa_analise_pf_endereco`<br>`bi_pessoa_analise_pf_fone`<br>`bi_pessoa_analise_pf_negativo`<br>`bi_pessoa_analise_pf_participacao` | `pessoa_analise_pf_email`<br>`pessoa_analise_pf_endereco`<br>`pessoa_analise_pf_fone`<br>`pessoa_analise_pf_negativo`<br>`pessoa_analise_pf_participacao` | `pessoa_analise_pf_id` |
| **Produtos** | `bi_produto` | `materiais`<br>`tabelas`<br>`unidades` | `bi_produto_lista_material`<br>`bi_produto_tabela`<br>`bi_produto_unidade` | `produto_lista_material`<br>`produto_produto_tabela`<br>`produto_produto_unidade` | `produto_id` |
| **Serviços** | `bi_servico` | `movimentos` | `bi_servico_movimento` | `servico_movimento` | `servico_id` |

---

## 🔀 Mapeamentos Polimórficos Dinâmicos

Para atender a tabelas compartilhadas por múltiplos tipos de transação, o sistema realiza uma verificação prévia no banco de dados para adaptar a estrutura de dados retornada.

### 1. Faturamento e Notas Fiscais (`fatur_nf` / `danfe`)
O sistema verifica o campo `tipo` da tabela `fatur_nf` para determinar a origem dos dados:

* **Quando `tipo = 'entrada'`**:
  * **Master**: `bi_fatur_nf_entrada`
  * **Laço de Parcelas**: Obtido de `bi_pagar` (com fallback em `financ_titulo`).
* **Quando `tipo = 'saida'`**:
  * **Master**: `bi_fatur_nf_saida`
  * **Laço de Parcelas**: Obtido de `bi_receber` (com fallback em `financ_titulo`).

*Ambos os tipos compartilham os laços de `produtos` (`bi_estoque_movimento`) e `servicos` (`bi_servico_movimento`).*

---

### 2. Pedidos, Orçamentos, Ordens de Serviço e PDV
A identificação do tipo do registro na tabela `pedido` define se o fluxo utilizará as estruturas de **Entrada** ou **Saída**.

#### Consulta Master Customizada
O sistema utiliza instruções SQL customizadas para carregar os registros master de pedidos (`bi_pedido_entrada` e `bi_pedido_saida`). Essas queries calculam campos em tempo de execução:
* `desconto_unificado`: Soma de `desconto_servico` e `desconto_produto`.
* `desc_perc_unificado`: Soma de `desconto_produto_perc` e `desc_perc_servico`.
* `total_geral`: Expressão matemática que soma `total_descritivo`, `total_servico` e `total_produto`.

#### Laços de Repetição Disponíveis para Pedidos

* **Híbridos (BI / Fallback)**:
  * `descritivos`: `bi_pedido_[entrada/saida]_descritivo` / `pedido_descritivo`
  * `parcelas`: `bi_pedido_[entrada/saida]_financ_titulo` / `pedido_financ_titulo`
  * `produtos`: `bi_pedido_[entrada/saida]_produto` / `pedido_produto`
  * `produtos_separar`: `bi_pedido_[entrada/saida]_produto_separar` / `pedido_produto`
  * `servicos`: `bi_pedido_[entrada/saida]_servico` / `pedido_servico`
  * `equipamentos`: `bi_pedido_equip` / `pedido_equip`
  * `perifericos`: `bi_pedido_equip_perifer` / `pedido_equip_perifer`
  * `pedido_image`: `bi_pedido_image` / `pedido_image`

* **Customizados via SQL Direto**:
  * `participantes`: Consulta os participantes adicionais associados na tabela `pedido_pessoa`.
  * `enderecos`: Executa joins nas tabelas de endereço (`pessoa_endereco`, `local_municipio`, `local_uf`, `local_pais`) para trazer dados estruturados de localização do cliente.
  * `produtos_condicao`: Filtra itens de produto da transação onde a coluna de condição corresponda a `TROUXE` ou `A TRAZER`.
  * `produtos_condicao_null`: Retorna apenas os produtos cuja condição seja nula (`IS NULL`).

---

## 🛠️ Enriquecimento Automático de Dados (`enrichFallbackData`)

Caso a rotina utilize tabelas físicas de fallback, o método `enrichFallbackData` é acionado para preencher informações essenciais ausentes na tabela de origem:

* **Produtos**: Obtém a descrição física da tabela `produto`, a sigla da unidade de medida de `produto_unidade` e a referência em `produto_referencia`.
* **Serviços**: Recupera a descrição correspondente da tabela `servico`.
* **Formas de Pagamento**: Preenche descrições a partir de `financ_forma_pgto`.
* **CNAE**: Busca o código e descrição da atividade econômica diretamente de `tributo_tab_atividade`.
* **Processos PCP**: Associa a descrição do processo de produção localizando a chave em `pcp_processo`.

---

## ⚙️ Regras de Formatação e Funções Especiais

* **Ordenação Dinâmica por Sufixo**: A utilização de sufixos como `_alfa` ou `_asc` na marcação de loops do HTML (ex: `<!--[produtos_alfa]-->`) sinaliza ao renderizador que os itens do array devem ser ordenados de forma alfabética com base no campo `descricao`.
* **Exposição de Equipamento Único**: Se a consulta de `equipamentos` retornar registros, os dados do primeiro item são transferidos diretamente para as variáveis globais de cabeçalho, dispensando a necessidade de abertura de laço no template HTML.
* **Variáveis de Acumulador (Soma)**: São geradas variáveis dinâmicas de totalização no cabeçalho como `{NOME_DO_LOOP_qtde_sum}` e `{NOME_DO_LOOP_total_sum}` com base nos valores identificados nos laços filhos correspondentes.
* **Formatador de Tipos**: Campos numéricos identificados como moeda ou quantidade são convertidos e exibidos de acordo com as regras de casas decimais definidas na tabela de parâmetros do sistema (`Global_Parametro`).
