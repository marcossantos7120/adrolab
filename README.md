# 🤖 Trossen Robotics - LeRobot Setup Guide (Stationary)

Este guia fornece as instruções essenciais para configurar e operar braços robóticos da Trossen Robotics utilizando a biblioteca **LeRobot** da Hugging Face.

---

## 🔑 1. Hugging Face Authentication

A autenticação é necessária para baixar modelos pré-treinados e realizar o upload de seus próprios datasets.

Para realizar o login:
```bash
hf auth login
```

Caso precise forçar a reautenticação ou trocar de conta:
```bash
hf auth login --force
```

---

## 🛠️ 2. Instalação e Setup (LeRobot)

Para configurar o suporte aos braços Trossen (ViperX, WidowX, etc.), siga os passos abaixo:

### 1. Clonar o Repositório

```bash
git clone https://github.com/huggingface/lerobot.git
cd lerobot
```

### 2. Instalar Dependências

Instale o pacote com o plugin específico para hardware Trossen:

```bash
pip install -e ".[trossen]"
```

### 3. Documentação de Referência

Para detalhes avançados, consulte o [Tutorial Oficial da Trossen Robotics](https://github.com/huggingface/lerobot).

---

## 🕹️ 3. Comandos de Operação

### Testar Conexão e Teleoperação

Use este comando para controlar o braço manualmente e validar a comunicação com os motores Dynamixel:

```bash
python lerobot/scripts/control_robot.py \
    --robot-path lerobot/configs/robot/trossen_vx250.yaml \
    --control-mode teleop
```

> **Nota:** Substitua `vx250` pelo modelo específico do seu braço, ex: `wx250`, `vx300`.

### Gravação de Datasets

Para gravar episódios de demonstração para treinamento de IA:

```bash
python lerobot/scripts/control_robot.py \
    --robot-path lerobot/configs/robot/trossen_vx250.yaml \
    --control-mode teleop \
    --save-episodes true
```

---

## ⚠️ Requisitos de Hardware (Stationary)

- **Conexão Serial:** O braço deve estar conectado via USB. Caso o sistema não reconheça, libere a porta no Linux:
  ```bash
  sudo chmod 666 /dev/ttyUSB0
  ```
- **Energia:** Certifique-se de que a fonte de 12V está conectada à placa controladora.
- **Segurança:** Fixe a base do robô firmemente na mesa *(Stationary)* antes de iniciar movimentos de alta velocidade.

---

*Última atualização: Abril de 2026*
