
import hashlib
import os

def verificar_usuario_existente(nome_arquivo, nome_usuario):
    with open(nome_arquivo, 'r') as arquivo:
        for linha in arquivo:
            dados = linha.strip().split(",")
            if dados[0] == nome_usuario:
                return True
        return False

def salvar_arquivo(nome_arquivo, nome_usuario, senha):
    if verificar_usuario_existente(nome_arquivo, nome_usuario):
        print("Este usuário já existe. Por favor, escolha outro nome de usuário.")
        return False
    else:
        salt = os.urandom(32)  # Gera um salt aleatório de 32 bytes
        hash_senha = hashlib.sha256(salt + senha.encode()).hexdigest()
        with open(nome_arquivo, 'a+') as arquivo:
            arquivo.write(f"{nome_usuario},{salt.hex()},{hash_senha}\n")
            print('Usuário criado com sucesso')
            return True

def ler_arquivo(nome_arquivo):
    with open(nome_arquivo, 'r') as arquivo:
        usuarios = []
        for linha in arquivo:
            usuario, salt, senha = linha.strip().split(",")
            usuarios.append((usuario, salt, senha))
    return usuarios

ARQUIVO = 'usuarios.txt'

while True:
    nome_usuario = input('Crie um login:')
    psw_usuario = input('Crie uma senha:')
    if salvar_arquivo(ARQUIVO, nome_usuario, psw_usuario):
        break

usuarios = ler_arquivo(ARQUIVO)

# Autenticação
login = input("Digite seu usuário:")
psw = input("Digite sua senha:")

autenticado = False

for usuario in usuarios:
    nome = usuario[0]
    salt = bytes.fromhex(usuario[1])
    senha = usuario[2]

    hash_senha2 = hashlib.sha256(salt + psw.encode()).hexdigest()

    if nome == login and senha == hash_senha2:
        autenticado = True
        break

if autenticado:
    print('Seja bem-vindo')
else:
    print('Usuário ou senha incorreto')
