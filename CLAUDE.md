# Pauta Violão

Leitura de partitura com o instrumento, para os alunos do Sidney (SESI-SP). Um único arquivo HTML (`index.html`), publicado pelo GitHub Pages, usa o microfone. Responda e escreva comentários em **português do Brasil**.

- Site: `https://rapososidney.github.io/pauta-violao/`
- Publicar = `git push` na `main`. O Pages leva 1–2 min, e o navegador pode manter respostas em cache por até 10 min (para conferir antes disso, use `?v=N` na URL).
- Faça commit só quando o Sidney pedir. Mensagens de commit em português.
- Este arquivo veio de `pauta-violao.html` no repositório antigo `Quizes-Musicais-Teste` (em aposentadoria). As regras comuns a todos os projetos estão no `CLAUDE.md` da pasta `Documentos\GitHub`.
- **Sem `manifest`/`sw.js`**: o arquivo de origem nunca foi instalável, então a migração manteve assim. Microfone exige https, e o GitHub Pages já é https (por isso o aviso de erro sugere hospedar lá se aberto como arquivo local).
- **Removida a dependência do Google Fonts** (`fonts.googleapis.com`, fontes Fraunces/Outfit) que havia no arquivo de origem — violava a regra "sem dependências externas, tem que abrir offline". O CSS já tinha fallback (`system-ui`/`serif`), então a página renderiza igual, só com a fonte do sistema no lugar da fonte baixada.

## Regras do repositório

- **Tudo no `index.html`, sem dependências externas**: sem CDN, sem build. Tem que abrir offline (exceto o microfone, que exige permissão e https).
- `localStorage['pautaViolao_tema']` guarda só o tema claro/escuro. **A chave era genérica (`"tema-quiz"`) no arquivo de origem** — foi trocada por esse prefixo na migração porque a origem `rapososidney.github.io` é compartilhada com os outros quizzes do Sidney, e uma chave genérica faria um quiz mudar o tema de outro. Nunca volte a uma chave sem prefixo.

## O app (index.html)

Notas nascem à direita e rolam para a esquerda sobre uma pauta em clave de sol (desenhada em SVG, `criarNota`/`montarCenario`); o aluno toca a nota correspondente no violão antes que ela saia da "zona" de resposta no centro da tela.

- **Detecção de altura por autocorrelação** (não é o YIN dos afinadores — método mais simples, adequado aqui porque só precisa da classe de altura (pitch class), não da afinação fina em cents). Portão de silêncio por RMS, busca de pico de autocorrelação com interpolação parabólica, faixa 70–1200 Hz. `getUserMedia` sem AGC/NS/EC, igual aos afinadores.
- Uma nota só "firma" após 4 quadros consecutivos com a mesma pitch class (`estCont`), para não disparar com transientes.
- Acerto = pitch class detectada bate com a nota na zona; tocar fora da zona não pune. Nota que escapa da zona sem resposta conta como erro e dispara a buzina (ruído dissonante grave, `buzina()` — sawtooth em 98/104 Hz, batimento grave, não é um bipe afinado).
- Velocidade de rolagem ajustável (`velRange`, 48–210 unidades/s). Placar: acertos, erros, sequência (zera a cada erro).
- `tocarMidi()` (tom triangular com detune, toca uma nota afinada) está definida mas não é chamada em nenhum lugar do código atual — parece recurso não concluído na origem; mantido como estava, sem ligar nem remover, para não mudar comportamento na migração.
- Tema claro/escuro com persistência em `localStorage` (ver acima).
