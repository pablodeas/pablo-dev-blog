---
title: "Backup e Upload com Zsh"
date: 2024-05-07T16:55:49-03:00
draft: false
toc: false
images:
tags:
  - Backup
  - Zsh
  - Shell
---

Na era digital de hoje, o backup e a sincronização de dados tornaram-se aspectos cruciais para a manutenção de nossas vidas digitais. Quer se trate de projetos pessoais, documentos de trabalho ou fotos de família, é fundamental garantir que nossos dados estejam seguros e facilmente acessíveis.

No tutorial de hoje, apresentarei um conjunto de scripts personalizados projetados para automatizar o processo de backup e upload de um diretório específico. Esses scripts, escritos em Zsh e Bash, utilizam ferramentas poderosas como `rsync`, `tar` e `rclone` para agilizar suas tarefas de backup e upload.

## Olhando de perto

### Script de Backup: `do_backup.zsh`

Nossa jornada começa com o script `do_backup.zsh`, um companheiro confiável para criar backups de seus diretórios especificados. 
Ele emprega `rsync` para espelhar arquivos do diretório de origem para um local de backup designado, seguido por uma compactação em um arquivo `.tar.gz` organizado. Este script não trata apenas de copiar arquivos; trata-se de proteger seus dados com o mínimo de esforço.

```shell
#!/usr/bin/env zsh

# Script Name:  backup.zsh
# Author:       Pablo Andrade
# Created:      28/11/2023
# Version:      1.2

# To decompress
#tar -xzvf $bkp_file

# Debugging ON/OFF
set -x

#script_path="$(dirname "${BASH_SOURCE[0]}")"

# Variables
project_directory="/home/pablodeas/Workspace/Projects/pessoal/do_backup"
source "$project_directory/config.sh"

# Remove Last Backup File
function remove_last () {
	echo " --- "
	echo "-> Removing last Backup File!..."
	echo " --- "
	/usr/bin/rm -f $bkp_last
	
	if [ $? -eq 0 ]; then
		echo $msg_sucess
	else
		echo $msg_error
	fi
}

# Backup
function exec_bkp () {
	echo " --- "
	echo "-> Starting Backup..."
	echo " --- "
	rsync -av --progress --partial --append-verify $main_dir $bkp_dir &> $project_log/rsync_$data.log

	if [ $? -eq 0 ]; then
		echo $msg_sucess
	else
		echo $msg_error
	fi
}

# Compress
function exec_compact () {
    echo " --- "
    echo "-> Starting Compression..."
    echo " --- "
    tar --remove-files -czvf $bkp_file * &> $project_log/tar_$data.log

    if grep -q "File shrank by" $project_log/tar_$data.log; then
        echo "-> Alert: The file was compressed Successfully, but there was an alert."
    elif [ $? -eq 0 ]; then
        echo $msg_sucess
    else
        echo $msg_error
    fi
}

# Execution
#1
remove_last

#2
exec_bkp

cd $bkp_dir

#3
exec_compact
```

#### Características principais:

- **Backup Eficiente**: Utiliza `rsync` para garantir um processo de backup rápido e eficiente.
- **Compressão**: Reduz o tamanho dos seus backups com a compressão `.tar.gz`.
- **Registro**: mantém um registro do processo de backup para referência futura.

#### Como usá-lo:

1. **Configuração**: Certifique-se de que seu arquivo `config.sh` esteja configurado corretamente com os caminhos para seus diretórios de origem e de backup.
2. **Execução**: Torne o script executável com `chmod +x do_backup.zsh` e execute-o com `./do_backup.zsh`.

### Script de Upload: `do_upload.zsh`

Seguindo o processo de backup, temos o script `do_upload.zsh`, que se encarrega de enviar seu arquivo de backup compactado para um local de armazenamento remoto.
Compatível com qualquer armazenamento remoto suportado por `rclone`, este script simplifica o processo de movimentação de seus backups para a nuvem.

```shell
#!/bin/zsh

# Script Name:    do_upload.sh
# Author:         Pablo Andrade
# Created:        07/12/2023
# Version:        0.2

# Debugging ON / OFF
#set -x

# Variables
#script_path="$(dirname "${BASH_SOURCE[0]}")"
project_directory="/home/pablodeas/Workspace/Projects/pessoal/do_backup"
source "$project_directory/config.sh"

# Upload
func exec_upload () {
  echo "-> Starting Upload..."
  rclone copy $bkp_dir $remote:/Backups/ -vv &> $project_log/rclone_$data.log

	if [ $? -eq 0 ]; then
		echo $msg_sucess
	else
		echo $msg_error
	fi
}

# Execution
exec_upload
```

#### Características principais:

- **Upload remoto**: aproveita o `rclone` para enviar seu arquivo de backup para uma solução de armazenamento remoto de sua escolha.
- **Logging**: Fornece logs do processo de upload, garantindo transparência.

#### Começando:

1. **Configuração**: Atualize seu arquivo `config.sh` com os detalhes de armazenamento remoto necessários.
2. **Preparação**: Execute `chmod +x do_upload.zsh` para tornar o script executável.
3. **Executar**: Basta executar `./do_upload.zsh` para iniciar o processo de upload.

## Nos Bastidores: Configuração e Dependências

Ambos os scripts dependem de um arquivo de configuração compartilhado, `config.sh`, que contém informações essenciais, como caminhos de diretório de origem e de backup, nomes de arquivos de backup e configurações de armazenamento remoto. Essa abordagem centralizada simplifica o gerenciamento de seus processos de backup e upload.

Além disso, os scripts dependem de diversas ferramentas:

- **Zsh e Bash**: as linguagens de script usadas para escrever os scripts.
- **rsync**: Essencial para o processo de backup.
- **tar**: Usado para compactar os arquivos de backup.
- **rclone**: Facilita o processo de upload para armazenamento remoto.

Antes de mergulhar nos scripts, certifique-se de que todas as dependências estejam instaladas e configuradas corretamente. Esta configuração permitirá que você automatize suas tarefas de backup e upload, economizando tempo e reduzindo o risco de perda de dados.

[Código Fonte](https://github.com/pablodeas/backup_with_zsh)

---

*Obrigado por lerem até aqui.*

Para entrar em contato comigo, utilize qualquer um dos links abaixo:

[Instagram](https://instagram.com/in/pablodeas)
.
[Whatsapp](https://api.whatsapp.com/send?phone=5521966916139)
.
[E-mail](mailto:pablodeas@gmail.com)