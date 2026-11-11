# CitizenTech — Assistente Municipal de Mobilidade Urbana

Projeto da Global Solution FIAP 2026.2 — Prompt and Artificial Intelligence, turma 1CCPG.

## Objetivo
Responder dúvidas sobre mobilidade urbana de Metrópole do Vale com base em documentos municipais simulados, citando as fontes, usando ferramentas para cálculos e aplicando guardrails.

## Arquivos
- `1_Notebook_GS2026_2.ipynb`: notebook completo.
- `2_base_documental/`: três documentos simulados escritos pela equipe.
- `3_avaliacao_ragas.json`: estado pendente até a execução real; o notebook o substitui por métricas medidas.
- `4_README.md`: documentação.
- `5_certificacao.txt`: identificação e links informados pela equipe.
- `6_Chat_Log.txt`: registro de uso de IA.
- `7_demo_video_url.txt`: campo para inserir o link real do vídeo.

## Arquitetura
```text
Documentos TXT
    ↓
Chunking (500/50 comparado com 1000/150)
    ↓
Sentence Transformers embeddings
    ↓
ChromaDB (metadados + cosine distance)
    ↓
Retriever (top-k) → Prompt com contexto → ChatOpenAI compatível com Ollama Cloud → resposta com fontes
    ↓
Agente tool-calling (status de ponto / cálculo de horário / consulta RAG)
    ↓
Guardrails → Gradio (histórico + painel de fontes e trace)
    ↓
RAGAS (8 perguntas, ground truths da equipe, métricas medidas)
```

## Armadilhas documentais
1. **Desatualização temporal:** a Ordem de Serviço de 12/05/2026 previa circulação da Linha 714 aos domingos; a Portaria de 18/10/2026 suspende essa circulação durante as obras.
2. **Interdição e acessibilidade:** o P-102 foi interditado e substituído pelo P-104; a informação da rampa de 6%, desnível zero e piso podotátil está no Anexo Técnico II.
3. **Faixa condicional:** o Decreto 63.412/2026 limita a Tarifa Zero de sábado na Linha 10-Saúde a pessoas com 65 anos ou mais, gestantes e PCDs, com vigência até 31/12/2026.

## Decisões e limites técnicos
- Chunking: são comparadas duas configurações reais; a tabela do notebook informa quantos chunks cada uma gerou.
- ChromaDB: metadados guardam `doc_id`, `data_publicacao`, `titulo`, `tema` e `chunk_id`.
- RAG: usa LCEL e uma chamada real ao Ollama Cloud. Documentos são tratados como dados, não como instruções.
- Agente: o modelo seleciona ferramentas via tool calling; `AgentExecutor` limita a cinco iterações.
- Cálculo de horário: é uma **estimativa derivada das faixas e frequências descritas** na Ordem de Serviço. O documento não fornece tabela completa de partidas; por isso o sistema informa essa limitação.
- Guardrails: filtro de escopo, detecção de prompt injection, bloqueio de solicitação de dados pessoais e recusa quando a recuperação não oferece evidência suficiente.
- RAGAS: o JSON desta versão está `pendente_de_execucao`. Não há métricas declaradas como medidas até a execução do notebook.

## Como executar no Google Colab
1. Extraia o ZIP mantendo a pasta `2_base_documental` junto do notebook, ou abra o notebook a partir do repositório GitHub.
2. No Colab, abra **Secrets** e crie `OLLAMA_API_KEY` com sua chave do Ollama Cloud.
3. Se necessário, crie também `OLLAMA_MODEL` com um modelo disponível na sua conta que suporte tool calling. O padrão é `gpt-oss:20b-cloud`, que deve ser confirmado como disponível na conta antes da execução.
4. Execute **Runtime → Run all**. A primeira célula instala as dependências e o modelo de embeddings pode ser baixado na primeira execução.
5. Na célula **Abrir a interface para demonstração**, descomente `demo.launch(share=True)` e execute a célula para abrir a interface Gradio no Colab.
6. Confirme que a seção 7 terminou e que `3_avaliacao_ragas.json` contém `status: executado` e valores numéricos.
7. Baixe o JSON atualizado e substitua o arquivo dentro do ZIP final.

## Verificações antes da entrega
- [ ] `OLLAMA_API_KEY` existe apenas em Secrets/variáveis de ambiente; nunca no notebook ou ZIP.
- [ ] O notebook executa do início ao fim com o modelo selecionado.
- [ ] Tool calling é executado de verdade e os traces mostram as ferramentas acionadas.
- [ ] Os quatro guardrails foram demonstrados.
- [ ] O JSON RAGAS contém resultados de execução real, não valores manuais.
- [ ] Ground truths foram revisados e confirmados pelos integrantes.
- [ ] Link e vídeo real de até 3 minutos foram inseridos.
- [ ] A equipe foi regularizada com o professor: o briefing permite dupla ou trio, mas os metadados atuais listam quatro integrantes.

## Aviso sobre composição da equipe
O briefing permite entrega em dupla ou trio e não permite grupos maiores. O material original identifica quatro integrantes. Não alteramos essa informação nem inventamos uma autorização; é necessário obter orientação formal do professor antes da entrega.
