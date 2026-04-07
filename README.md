# 🤖 Trossen Robotics — LeRobot Setup Guide (Stationary)

> Instruções essenciais para configurar e operar braços robóticos da Trossen Robotics utilizando a biblioteca **LeRobot** da Hugging Face.

---

## 📋 Índice

1. [Miniconda Setup](#-1-miniconda-setup)
2. [Instalação e Setup (LeRobot)](#️-2-instalação-e-setup-lerobot)
3. [Configurando Trossen AI](#️-3-configurando-trossen-ai)
4. [Teleoperação](#️-4-teleoperação)
5. [Hugging Face Authentication](#-5-hugging-face-authentication)
6. [Gravação de Datasets](#-6-gravação-de-datasets)
7. [Replay de Episódio](#-7-replay-de-episódio)
8. [Treinamento e Avaliação](#-8-treinamento-e-avaliação)

---

## 🐍 1. Miniconda Setup

O Miniconda é recomendado para isolar o ambiente Python do LeRobot e evitar conflitos de dependências.

Baixe e execute o instalador para Linux:

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

Siga as instruções na tela e reinicie o terminal ao finalizar. Para verificar a instalação:

```bash
conda --version
```

Crie um ambiente dedicado para o LeRobot com Python 3.10 e ative-o:

```bash
conda create -n lerobot python=3.10 -y
conda activate lerobot
```

> **Importante:** Execute `conda activate lerobot` sempre que abrir um novo terminal antes de usar o LeRobot.

---

## 🛠️ 2. Instalação e Setup (LeRobot)

> Certifique-se de que o ambiente `lerobot` está ativo (`conda activate lerobot`) antes de prosseguir.

Antes de prosseguir, faça a instalação do `uv`:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Ou, se não funcionar:

```bash
wget -qO- https://astral.sh/uv/install.sh | sh
```

Clone o repositório e instale os pacotes:

```bash
git clone https://github.com/huggingface/lerobot.git
cd lerobot
```

```bash
cd ~/lerobot_trossen
uv sync
```

Para verificar a instalação:

```bash
uv pip list | grep lerobot
```

---

## ⚙️ 3. Configurando Trossen AI

Nesta etapa, é essencial configurar os arquivos do LeRobot para que as câmeras sejam conectadas e para garantir a conexão correta dos braços do Stationary.

Navegue até o arquivo de configuração:

```
lerobot/common/robot_devices/robots/configs.py
```

No arquivo `configs.py`, configure o endereço IP correto para cada **Leader** e **Follower**, e insira o número serial de cada câmera RealSense no campo correspondente.

Para verificar o número serial de cada câmera, execute:

```bash
realsense-viewer
```

---

## 🕹️ 4. Teleoperação

Se a configuração ocorreu corretamente, será possível teleoperar o Stationary com o comando abaixo:

```bash
python lerobot/scripts/control_robot.py \
    --robot.type=trossen_ai_stationary \
    --robot.max_relative_target=5
```

---

## 🔑 5. Hugging Face Authentication

A autenticação é necessária para baixar modelos pré-treinados e realizar upload de datasets.

```bash
hf auth login
```

Caso precise forçar a reautenticação ou trocar de conta:

```bash
hf auth login --force
```

> **Importante:** Ao criar um token, certifique-se de que ele possui no mínimo permissões de **WRITE**.

---

## 💾 6. Gravação de Datasets

Grava episódios de demonstração para treinamento de IA. É necessário alterar os números seriais das câmeras e os IPs antes de executar:

```bash
uv run lerobot-record \
    --robot.type=bi_widowxai_follower_robot \
    --robot.left_arm_ip_address=192.168.1.5 \
    --robot.right_arm_ip_address=192.168.1.4 \
    --robot.id=bimanual_follower \
    --robot.cameras='{
    cam_high: {"type": "intelrealsense", "serial_number_or_name": "0123456789", "width": 640, "height": 480, "fps": 30},
    cam_low: {"type": "intelrealsense", "serial_number_or_name": "0123456789", "width": 640, "height": 480, "fps": 30},
    cam_left_wrist: {"type": "intelrealsense", "serial_number_or_name": "0123456789", "width": 640, "height": 480, "fps": 30},
    cam_right_wrist: {"type": "intelrealsense", "serial_number_or_name": "0123456789", "width": 640, "height": 480, "fps": 30},
    }' \
    --teleop.type=bi_widowxai_leader_teleop \
    --teleop.left_arm_ip_address=192.168.1.3 \
    --teleop.right_arm_ip_address=192.168.1.2 \
    --teleop.id=bimanual_leader \
    --display_data=true \
    --dataset.repo_id=${HF_USER}/bimanual-widowxai-handover-cube \
    --dataset.episode_time_s=60 \
    --dataset.reset_time_s=15 \
    --dataset.num_episodes=2 \
    --dataset.push_to_hub=true \
    --dataset.single_task="Grab and handover the red cube to the other arm" \
    --dataset.num_image_writer_threads_per_camera=8 \
    --display_data=false
```

---

## 🔁 7. Replay de Episódio

```bash
uv run lerobot-replay \
    --robot.type=bi_widowxai_follower_robot \
    --robot.left_arm_ip_address=192.168.1.5 \
    --robot.right_arm_ip_address=192.168.1.4 \
    --robot.id=bimanual_follower \
    --dataset.repo_id=${HF_USER}/<dataset-id> \
    --dataset.episode=0
```

---

## 🧠 8. Treinamento e Avaliação

### Executando o Treinamento

```bash
uv run lerobot-train \
    --dataset.repo_id=${HF_USER}/trossen_ai_stationary_test \
    --policy.type=act \
    --output_dir=outputs/train/act_trossen_ai_stationary_test \
    --job_name=act_trossen_ai_stationary_test \
    --policy.device=cuda \
    --wandb.enable=true \
    --policy.repo_id=${HF_USER}/my_policy
```

### Avaliando a Política de Treinamento

```bash
uv run lerobot-record \
    --robot.type=bi_widowxai_follower_robot \
    --robot.left_arm_ip_address=192.168.1.5 \
    --robot.right_arm_ip_address=192.168.1.4 \
    --robot.id=bimanual_follower \
    --robot.cameras='{
        cam_high: {type: intelrealsense, serial_number_or_name: "0123456789", width: 640, height: 480, fps: 30},
        cam_low: {type: intelrealsense, serial_number_or_name: "0123456789", width: 640, height: 480, fps: 30},
        cam_left_wrist: {type: intelrealsense, serial_number_or_name: "0123456789", width: 640, height: 480, fps: 30},
        cam_right_wrist: {type: intelrealsense, serial_number_or_name: "0123456789", width: 640, height: 480, fps: 30}
        }' \
    --display_data=false \
    --dataset.repo_id=${HF_USER}/eval_trossen_ai_stationary_test \
    --dataset.single_task="Grab and handover the red cube to the other arm" \
    --policy.path=${HF_USER}/my_policy
```

---

*Última atualização: Abril de 2026*