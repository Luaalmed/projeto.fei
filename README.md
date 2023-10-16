# projeto.fei
Projeto de Fundamentos de Algoritmo, desenvolvido por Luana Almeida e Bianca Silva do Centro Universitário FEI.

#Função para a opção 1 
def clientes():
  print("Acesse sua conta ou cadastre-se:")
  print("")
  nome = input("Digite a Razão Social da Empresa: ")
  cnpj = int(input("Digite o CNPJ: "))
  conta = input("Digite o Tipo de Conta: ") #comum ou plus
  valor = float(input("Digite o Valor Inicial da Conta: "))
  senha = int(input("Digite Sua Senha: "))

#Função para a opção 2
def apagar_cliente():
    cnpj = int(input("Digite Seu CNPJ Para Apagar Cadastro:"))
    print("")
    print("Cadastro Apagado!")

#Função para a opção 3 - Listar Clientes
def listar():
  print("lista")

#Função para a opção 4
def debito():
  cnpj = int(input("Digite o CNPJ: "))
  senha = int(input("Digite Sua Senha: "))
  valor = float(input("Digite o Valor Que Deseja Debitar: "))
  print("")
  print("Valor debitado da conta com sucesso!")

#Função para a opção 5
def deposito():
  cnpj = int(input("Digite o CNPJ: "))
  valor = float(input("Digite o Valor Que Deseja Depositar: "))
  print("")
  print("Depósito efetuado com sucesso!")

#Função para a opção 6
def extrato():
  cnpj = int(input("Digite o CNPJ: "))
  senha = int(input("Digite Sua Senha: "))
  print("Extrato emitido!")

# Função para a opção 7
def transferencia():
  cnpj = int(input("Digite o CNPJ de Origem: "))
  senha = int(input("Digite Sua Senha: "))
  cnpj = int(input("Digite o CNPJ de Destino: "))
  valor = float(input("Digite o Valor Que Deseja Transferir: "))

#Função para a opção 8
def livre():
  print("Opção 8")

#menu inicial/ criar um while e break
while (True):
  print("")
  print("1: Login / Novo Cadastro" )
  print("2: Excluir Cadastro")
  print("3: Listar Clientes")
  print("4: Débito")
  print("5: Deposito")
  print("6: Extrato")
  print("7: Transferência")
  print("8: Livre")
  print("9: Sair") 
  print("")
  menu = int(input("Digite a opção desejada: "))
  if menu == 9:
    break
  elif menu == 1: 
    clientes()
  elif menu == 2:
    apagar_cliente()
  elif menu == 3:
    listar()
  elif menu == 4:
    debito()
  elif menu == 5:
    deposito()
  elif menu == 6:
    extrato()
  elif menu == 7:
    transferencia()
  elif menu == 8:
    livre()
#para 
