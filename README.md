# Projeto Yocto: QEMU x86-64 Hello World

Este projeto demonstra a criação e execução de uma imagem Linux mínima para a arquitetura QEMU x86-64 usando o branch Scarthgap do Projeto Yocto. Ele inclui todas as etapas necessárias para configurar, construir e executar a imagem.

---

## Índice

1. [Sobre o Projeto](#sobre-o-projeto)
1. [Status-do-Projeto](#status-do-projeto)
1. [Introdução](#introdução-do-projeto)
1. [Dependências](#dependências)
1. [Obtendo a Fonte](#obtendo-a-fonte)
1. [Construindo](#construindo)
1. [Executando Testes](#executando-testes)
1. [Instalação](#instalação)
1. [Uso](#uso)
1. [Problemas Comuns Durante a Compilação](#problemas-comuns-durante-a-compilação)
1. [Processo de Lançamento](#processo-de-lançamento)
1. [Controle de Versão](#controle-de-versão)
1. [Como Obter Ajuda](#como-obter-ajuda)
1. [Contribuindo](#contribuindo)
1. [Leitura adicional](#leitura-adicional)
1. [Licença](#licença)
1. [Autores](#autores)
1. [Agradecimentos](#agradecimentos)

---

## Sobre o projeto

Este projeto foi criado para:

- Construir uma imagem Linux mínima utilizando a branch Scarthgap do Projeto Yocto.
- Visar a arquitetura QEMU x86-64 para testes de hardware emulado.
- Fornecer um ponto de partida para aprender e experimentar com o Projeto Yocto.

Os recursos incluem:

- Uma imagem simples "Hello World" para QEMU.
- Sistema de construção modular e personalizável.
- Suporte para ambientes de desenvolvimento virtualizados.

Exemplo de comando para executar a imagem construída após uma compilação bem-sucedida do bitbake:

```bash
runqemu qemux86-64
```

**[Voltar ao topo](#table-of-contents)**

---

## Status do projeto

[![Status da construção](http://your-server:12345/job/badge/icon)](http://your-server/job/badge/icon/)

- Versão atual: **v1.0.0**
- Totalmente funcional para QEMU x86-64 com scripts de construção validados.
- Problema conhecido: compatibilidade limitada com versões do sistema operacional host não listadas em dependências.

**[Voltar ao topo](#table-of-contents)**

---

## Primeiros passos

Estas instruções ajudarão você a configurar o projeto e construir a imagem.

### Dependências

Instale o seguinte no seu sistema host:

```bash
sudo apt update && sudo apt install -y \
bc build-essential chrpath cpio diffstat gawk git texinfo wget \
gdisk python3 python3-pip zlib1g-dev libncurses5-dev libncursesw5-dev \
libssl-dev liblz4-tool locales libacl1
```

Certifique-se de que o local UTF-8 seja gerado:

```bash
sudo locale-gen en_US.UTF-8
```

### Obtendo a fonte

Clone o repositório do projeto:

```bash
git clone git://git.yoctoproject.org/poky.git
cd poky
git checkout scarthgap-5.0.5
```

### Construindo

1. Defina o ambiente de construção:

```bash
source oe-init-build-env
```

2. Configure o ambiente de construção em `conf/local.conf` e `conf/bblayers.conf`:

- Certifique-se de que as seguintes camadas estejam incluídas:
```
BBLAYERS ?= " \
/path/to/poky/meta \
/path/to/poky/meta-poky \
/path/to/poky/meta-yocto-bsp \
"
```

3. Crie a imagem mínima:

```bash
bitbake core-image-minimal
```

### Executando testes

Execute o QEMU para inicializar a imagem gerada:

```bash
runqemu qemux86-64
```

#### Outros testes

Opcional: adicione testes adicionais ou ferramentas de análise estática conforme necessário.

### Instalação

Copie a imagem gerada para o ambiente ou dispositivo desejado.

```bash
cp tmp/deploy/images/qemux86-64/core-image-minimal-qemux86-64.ext4 /path/to/output/
```

### Uso

Execute a imagem no QEMU:

```bash
runqemu qemux86-64
```

**[Voltar ao topo](#table-of-contents)**

---

## Problemas comuns durante a compilação

Durante o processo de configuração e compilação do projeto, os seguintes problemas foram encontrados e resolvidos:

1. **PermissionError durante a compilação**:
- **Problema**: erros de permissão ocorreram ao gravar em diretórios específicos durante tarefas `bitbake`.
- **Solução**: Garantiu que todos os diretórios no projeto eram de propriedade do usuário atual usando:
```bash
sudo chown -R $(whoami):$(whoami) /path/to/project
chmod -R u+rwX /path/to/project
```

2. **Aviso de validação da distribuição do host**:
- **Problema**: Um aviso indicou que a distribuição do host (Ubuntu 24.04) não foi validada.
- **Solução**: Rebaixado para o Ubuntu 22.04, um sistema operacional host validado para a ramificação Yocto Scarthgap.

3. **Falha na compilação do GCC**:
- **Problema**: A compilação do `gcc-13.3.0` falhou devido a dependências ausentes ou problemas de configuração.
- **Solução**: Resolvido por:
- Instalando dependências adicionais:
```bash
sudo apt install -y zlib1g-dev libncurses5-dev libssl-dev
```
- Tentando novamente o processo de construção `bitbake` várias vezes.
- Limpando e reconstruindo a tarefa GCC:
```bash
bitbake -c cleanall gcc
bitbake core-image-minimal
```

4. **Versão preferida não disponível**:
- **Problema**: Definir `PREFERRED_VERSION_gcc = "12.%"` em `local.conf` resultou em avisos porque a versão 12 não estava disponível.
- **Solução**: Mantido GCC 13.3.0, a versão padrão, após confirmar a compatibilidade com o branch Scarthgap.

**[Voltar ao topo](#table-of-con
