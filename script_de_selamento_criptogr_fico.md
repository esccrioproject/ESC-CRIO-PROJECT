#!/usr/bin/env bash
# Script de Ancoragem e Verificação de Integridade - Projeto Cryo (ESCCRIO)
# Arquiteto e Guardião: André Urquiza Scarazzato

set -euo pipefail

ARQUIVO="TRATADO_CRYO_v1.0.md"
MANIFEST_HASH="TRATADO_CRYO_v1.0.sha256"

if [ ! -f "$ARQUIVO" ]; then
    echo "Erro: Arquivo $ARQUIVO não encontrado no diretório atual."
    exit 1
fi

echo "=== Iniciando Selamento Criptográfico do Tratado Cryo ==="

# 1. Geração do Hash SHA-256 Canônico
sha256sum "$ARQUIVO" > "$MANIFEST_HASH"
HASH_VALOR=$(awk '{print $1}' "$MANIFEST_HASH")

echo "[OK] Hash SHA-256 gerado: $HASH_VALOR"
echo "[OK] Manifesto salvo em: $MANIFEST_HASH"

# 2. Verificação de Git e preparação para commit assinado
if [ -d ".git" ]; then
    git add "$ARQUIVO" "$MANIFEST_HASH"
    echo "[OK] Arquivos preparados no Git. Execute 'git commit -S -m \"Selo Ontológico: Tratado Cryo v1.0\"' para assinar."
else
    echo "[INFO] Inicialize um repositório Git ('git init') para versionamento distribuído."
fi

# 3. Instrução para OpenTimestamps (se instalado)
if command -v ots &> /dev/null; then
    ots stamp "$MANIFEST_HASH"
    echo "[OK] Carimbo de tempo OpenTimestamps gerado (${MANIFEST_HASH}.ots)."
else
    echo "[DICA] Para ancoragem pública em blockchain sem custo, instale o 'opentimestamps-client' e execute: ots stamp $MANIFEST_HASH"
fi

echo "=== Processo concluído. Anterioridade pronta para publicação pública. ==="