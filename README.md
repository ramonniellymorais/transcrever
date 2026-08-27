# transcrever

Transforma reunião, aula, mentoria ou podcast em roteiro legível — **com identificação de quem fala** — rodando inteiro no seu Mac.

```bash
transcrever ~/Downloads/mentoria.mp4
```

Sai isto:

```
TRANSCRIÇÃO — mentoria
Locutores detectados: 2 (SPEAKER_00, SPEAKER_01)
────────────────────────────────────────────────────────────

[00:00] SPEAKER_00
Então, a primeira coisa que eu queria entender é como você está
organizando o calendário hoje.

[00:14] SPEAKER_01
Hoje eu faço tudo na véspera. E aí acontece o que sempre acontece,
eu publico o que dá tempo, não o que eu tinha planejado.
```

---

## Por que isso existe

O maior desperdício de matéria-prima em conteúdo é a call gravada que ninguém reabre. Duas horas de mentoria têm dentro a história, o número, a objeção real do cliente e a frase que ele repete sem perceber — tudo trancado num arquivo que ninguém tem paciência de reassistir.

Transcrição corrida resolve pela metade, porque some quem falou o quê. Numa entrevista ou numa mentoria, isso é justamente o que importa: separar a pergunta da resposta, o especialista do cliente, a fala que vira conteúdo da fala que era só contexto.

Três coisas que mudam na prática:

- **O áudio não sai da sua máquina.** Nada de material de cliente subindo para servidor de terceiro.
- **Sem mensalidade e sem limite de minutos.** Você pode jogar o acervo inteiro de uma vez.
- **A saída já vem pronta para ler**, agrupada por locutor, em vez de mil linhas de legenda picada.

---

## Instalar

```bash
brew install ffmpeg@7
pipx install whisperx

git clone https://github.com/ramonniellymorais/transcrever.git
cd transcrever
chmod +x transcrever consolidar-transcricao
cp transcrever consolidar-transcricao ~/.local/bin/
```

Se `~/.local/bin` ainda não estiver no seu caminho:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc
```

### Ativar a separação de locutores

A transcrição funciona sem isso, mas entrega texto corrido. Para separar as vozes, são três passos, todos gratuitos:

1. Crie um token de leitura em [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)
2. Aceite os termos em [pyannote/speaker-diarization-community-1](https://huggingface.co/pyannote/speaker-diarization-community-1)
3. Guarde o token como variável de ambiente:

```bash
echo 'export HF_TOKEN=hf_xxxxx' >> ~/.zshrc && source ~/.zshrc
```

> O token entra por variável de ambiente de propósito. Chave escrita dentro de arquivo é chave que um dia vai parar num repositório público, e robôs varrem o GitHub procurando exatamente isso.

---

## Usar

```bash
# padrão: melhor qualidade, identificando quem fala
transcrever ~/Downloads/mentoria.mp4

# duas vezes mais rápido, qualidade um pouco menor
transcrever ~/Downloads/podcast.mp3 medium

# quando você já sabe quantas pessoas falam, diga — fica bem mais preciso
transcrever ~/Downloads/call.m4a large-v3 --falantes 2
```

Aceita o que o ffmpeg aceita: `mp4`, `mov`, `mp3`, `m4a`, `wav`, `aac` e companhia.

### O que é gerado

Tudo na mesma pasta do arquivo de entrada:

| Arquivo | Para que serve |
|---|---|
| **`nome (roteiro).txt`** | **O principal.** Falas agrupadas por locutor, com marcação de tempo. É o que você abre para trabalhar. |
| `nome.txt` | Texto corrido, para busca e para colar em outra ferramenta. |
| `nome.srt` / `nome.vtt` | Legenda, para subir junto com o vídeo. |
| `nome.json` | Dados completos com tempo por trecho, para quem for processar depois. |
| `nome.tsv` | Tabela, para abrir em planilha. |

### Modelos

| Modelo | Quando usar |
|---|---|
| `large-v3` | Padrão. Use sempre que a transcrição for virar conteúdo. |
| `medium` | Cerca de duas vezes mais rápido. Bom para triagem de acervo grande. |
| `small` | Rascunho rápido, só para saber se vale a pena transcrever direito. |

---

## Depois da transcrição

O arquivo `(roteiro).txt` é matéria-prima, ainda não é conteúdo. O que eu faço em seguida:

1. **Leio procurando a frase que a pessoa repete sem perceber.** Costuma ser o bordão real da marca, e quase nunca é o que ela diria se você perguntasse qual é o bordão dela.
2. **Marco a objeção dita em voz alta.** Objeção que aparece em call é objeção real; a que aparece em pesquisa de formulário é a versão editada.
3. **Separo história de explicação.** A explicação vira carrossel. A história vira reels, e vale muito mais.

Se for usar IA nesse passo, entregue o roteiro consolidado, não a legenda picada — o resultado muda bastante.

---

## Se der problema

**"whisperx: command not found"** — o `pipx ensurepath` não rodou ou o Terminal não foi reaberto. Rode `pipx ensurepath`, feche e abra o Terminal.

**Transcreveu, mas não separou os locutores** — falta o `HF_TOKEN`, ou os termos do modelo pyannote não foram aceitos. Os dois passos estão acima.

**Erro citando ffmpeg, torchcodec ou libavcodec** — é a incompatibilidade com o ffmpeg 8. Rode `brew install ffmpeg@7`. O script encontra a versão 7 sozinho e passa a usar ela.

**Está muito devagar** — a primeira execução baixa o modelo (alguns gigas) e demora bem mais que as seguintes. Se continuar lento depois disso, use `medium`.

**Cortou os locutores errado** — passe `--falantes N` com o número real de vozes. É o ajuste que mais melhora o resultado.

---

## Continua

- **[stack-marketing-com-ia](https://github.com/ramonniellymorais/stack-marketing-com-ia)** — o resto do que está instalado aqui e para que serve
- **[setup-mac-estrategista](https://github.com/ramonniellymorais/setup-mac-estrategista)** — instalar do zero, na ordem certa
- **[checar-copy](https://github.com/ramonniellymorais/checar-copy)** — verificador que reprova copy genérica

---

Construído em cima do [WhisperX](https://github.com/m-bain/whisperX) (Max Bain) e do [pyannote.audio](https://github.com/pyannote/pyannote-audio). O mérito da parte difícil é deles; aqui é o embrulho que faz caber no dia de trabalho.

Feito por **[Ramonnielly Morais](https://ramonniellymorais.com.br)**, criadora do Método ELO Criativo.

Licença [MIT](LICENSE).
