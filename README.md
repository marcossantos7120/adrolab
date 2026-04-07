# 🤖 Trossen Robotics — LeRobot Setup Guide (Stationary)

> Instruções essenciais para configurar e operar braços robóticos da Trossen Robotics utilizando a biblioteca **LeRobot** da Hugging Face.

---

## 📋 Índice

1. [Miniconda Setup](#-1-miniconda-setup)
2. [Hugging Face Authentication](#-2-hugging-face-authentication)
3. [Instalação e Setup (LeRobot)](#️-3-instalação-e-setup-lerobot)
4. [Comandos de Operação](#️-4-comandos-de-operação)
5. [Requisitos de Hardware](#️-5-requisitos-de-hardware-stationary)

---

## 🐍 1. Miniconda Setup

O Miniconda é recomendado para isolar o ambiente Python do LeRobot e evitar conflitos de dependências.

### 1.1 Instalar o Miniconda

Baixe e execute o instalador para Linux:

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

Siga as instruções na tela e reinicie o terminal ao finalizar. Para verificar a instalação:

```bash
conda --version
```

### 1.2 Criar o Ambiente Virtual

Crie um ambiente dedicado para o LeRobot com Python 3.10:

```bash
conda create -n lerobot python=3.10 -y
```

### 1.3 Ativar o Ambiente

```bash
conda activate lerobot
```

> **Importante:** Execute este comando sempre que abrir um novo terminal antes de usar o LeRobot.

### 1.4 Desativar o Ambiente (quando necessário)

```bash
conda deactivate
```


## 🛠️ 3. Instalação e Setup (LeRobot)

> Certifique-se de que o ambiente `lerobot` está ativo (`conda activate lerobot`) antes de prosseguir.
Antes de prosseguir, faca a instalacao do uv:
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```
ou, se nao funcionar:

```bash
wget -qO- https://astral.sh/uv/install.sh | sh
```

### 3.1 Clonar o Repositório

```bash
git clone https://github.com/huggingface/lerobot.git
cd lerobot
```

### 3.2 Instalar os pacotes do lerobot e as suas dependencias

```bash
cd ~/lerobot_trossen
uv sync
```
Para verificar a instalacao, faca:
```bash
cd ~/lerobot_trossen
uv pip list | grep lerobot
```

## ⚙️ 4. Configurando Trossen AI

Nesta etapa, é essencial configurar os arquivos do LeRobot para que as câmeras sejam conectadas. Além disso, essa etapa garante a conexão correta dos braços do Stationary.

### 4.1 Editando o arquivo `configs.py`

Navegue até o seguinte caminho dentro do repositório clonado:

```bash
lerobot/common/robot_devices/robots/configs.py
```


### 4.2 Configurando os IPs (Leader/Follower)

No arquivo `configs.py`, localize os campos correspondentes a cada braço e configure o endereço IP correto para cada **Leader** e **Follower**.

### 4.3 Configurando as Câmeras RealSense

Para verificar o número serial de cada câmera, execute:

```bash
realsense-viewer
```

No mesmo arquivo `configs.py`, insira o número serial encontrado para cada câmera no campo correspondente.



## 🕹️ 5. Teleoperacao 

Nesta etapa, teste. Se a configuração ocorreu corretamente será possível teleoperar o Stationary
```bash
python lerobot/scripts/control_robot.py \
    --robot.type=trossen_ai_stationary \
    --robot.max_relative_target=5
```


### 5.1 Testar Conexão e Teleoperação

Controla o braço manualmente e valida a comunicação com os motores Dynamixel:

```bash
python lerobot/scripts/control_robot.py \
    --robot-path lerobot/configs/robot/trossen_vx250.yaml \
    --control-mode teleop
```

> **Nota:** Substitua `vx250` pelo modelo do seu braço — ex: `wx250`, `vx300`.

### 5.2 Gravação de Datasets

Grava episódios de demonstração para treinamento de IA:

```bash
python lerobot/scripts/control_robot.py \
    --robot-path lerobot/configs/robot/trossen_vx250.yaml \
    --control-mode teleop \
    --save-episodes true
```

---

## 🔑 2. Hugging Face Authentication

A autenticação é necessária para baixar modelos pré-treinados e realizar upload de datasets.

Login padrão:

```bash
hf auth login
```

Forçar reautenticação ou trocar de conta:

```bash
hf auth login --force
```
## ⚠️ 5. Requisitos de Hardware (Stationary)

| Item | Detalhe |
|---|---|
| **Conexão Serial** | Braço conectado via USB. Caso não reconhecido, libere a porta: `sudo chmod 666 /dev/ttyUSB0` |
| **Energia** | Fonte de 12V conectada à placa controladora |
| **Segurança** | Base do robô fixada firmemente na mesa antes de movimentos de alta velocidade |

---

*Última atualização: Abril de 2026*
