# lista que vai armazenar os clientes cadastrados
lista_cliente = []

# Reprodução do Nome do Banco para a Pagina Inicial
print("Banco QuemPoupaTem")

#xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxFUNÇÃO-01: CADASTROxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


def clientes(lista_cliente):
  print("")
  print("Acesse sua conta ou cadastre-se:")
  print("")

  # Solicitações das informações necessárias para o Cadastro
  nome = input("Digite a Razão Social da Empresa: ")

  # Para que só seja possível continuar se o CNPJ tiver 14 digitos
  while True:
    cnpj = input("Digite o CNPJ: ")
    if len(cnpj) == 14 and cnpj.isdigit():
      break
    else:
      print("O CNPJ deve ter exatamente 14 dígitos. Tente novamente.")
  conta = input("Digite o Tipo de Conta (Comum / Plus): ")
  valor = float(input("Digite o Valor Inicial da Conta: "))
  senha = int(input("Digite Sua Senha: "))

  # Armezanando as informações cadastradas no arquivo txt juntamente com as respectivas solicitações.
  cliente = {
      "Nome": nome,
      "CNPJ": cnpj,
      "Conta": conta,
      "Valor": valor,
      "Senha": senha
  }
  # Para adicionar as informações na lista
  lista_cliente.append(cliente)

  # Adicionando as informações no arquivo clientes.txt
  with open("clientes.txt", "a") as arquivo:
    arquivo.write(
        f"Nome: {nome}\nCNPJ: {cnpj}\nConta: {conta}\nValor: {valor}\nSenha: {senha}\n\n"
    )
  print("Cadastro efetuado com sucesso!")

  # Abertura de arquivos para armazenar as operações no arquivo txt que ira ser utilizado na função de extrato
  with open(f"{cliente['CNPJ']}_operacoes.txt", "w") as arquivo_operacoes:
    arquivo_operacoes.write("Operações do cliente:\n")
  # Abertura de arquivos para armazenar os clientes no arquivo txt que ira ser utilizado na função livre
  with open(f"{cliente['CNPJ']}_clientes.txt", "w") as arquivo_clientes:
    arquivo_clientes.write("Clientes do Cliente:\n")


#xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxFUNÇÃO-02: APAGARxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


def apagar_cliente(lista_cliente):
  cnpj2 = input("Digite Seu CNPJ Para Apagar Cadastro:")

  # Para percorrer a lista afim de confirmar se o cnpj digitado corresponde a algum cnpj presente na lista de clientes.
  for cliente in lista_cliente:
    if cliente["CNPJ"] == cnpj2:
      # O remove faz com que aquele
      lista_cliente.remove(cliente)
      print("Cadastro apagado!")
      break
  else:
    print("CNPJ não encontrado na base de dados.")

  # Abertura do Arquivo para que o cliente que pedimos para ser apagado seja apagado do arquivo principal
  with open("clientes.txt", "w") as arquivo:
    for cliente in lista_cliente:
      arquivo.write(
          f"Nome: {cliente['Nome']}\nCNPJ: {cliente['CNPJ']}\nConta: {cliente['Conta']}\nValor: {cliente['Valor']}\nSenha: {cliente['Senha']}\n\n"
      )


#xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxFUNÇÃO-03: LISTARxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


def listar():
  try:
    # Abertura do arquivo txt em formato de leitura para que todos os clientes sejam listados no console.
    with open("clientes.txt", "r") as arquivo:
      conteudo = arquivo.read()
      # Prints para que primeiro apareça a infomação de que aquela é a lista de clientes e que abaixo esta o conteúdo da lista
      print("Lista de clientes:")
      print(conteudo)
  # Caso não tenha nenhum cliente cadastrado
  except FileNotFoundError:
    print("Arquivo 'clientes.txt' não encontrado.")


#xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxFUNÇÃO-04: DÉBITOxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


def debito(lista_cliente, cnpj3, senha2, valor2):
  cliente_encontrado = None

  # Abertura do arquivo para leitura
  with open("clientes.txt", "r") as arquivo:
    dados_arquivo = arquivo.readlines()

  # Para percorrer a lista e numerar o indice especifico onde esta o cnpj
  indice_cnpj = -1
  for i, linha in enumerate(dados_arquivo):
    if linha.startswith(f"CNPJ: {cnpj3}"):
      indice_cnpj = i
      break

  # Verificar se o CNPJ foi encontrado
  if indice_cnpj != -1:
    # Inicializar variáveis
    senha_arquivo = None
    valor_cliente = None
    tipo_conta = None

    # Verificar as informações correspondentes ao cliente: valor, conta e senha
    for j in range(indice_cnpj, min(indice_cnpj + 5, len(dados_arquivo))):
      if "Senha" in dados_arquivo[j]:
        senha_arquivo = int(dados_arquivo[j].split(":")[1].strip())
      elif "Valor" in dados_arquivo[j]:
        valor_cliente = float(dados_arquivo[j].split(":")[1].replace(
            ",", ".").strip())
      elif "Conta" in dados_arquivo[j]:
        tipo_conta = dados_arquivo[j].split(":")[1].strip()

    # Verificar se todas as informações necessárias foram encontradas
    if senha_arquivo is not None and valor_cliente is not None and tipo_conta is not None:
      if senha_arquivo == senha2:
        # Senha válida, prosseguir com o débito
        taxa = 0.05 if tipo_conta.lower() == "comum" else 0.03
        print(f"Taxa aplicada: {taxa*100}%")

        valor_a_debitar = valor2 + (taxa * valor2)
        limite_negativo = -1000 if tipo_conta.lower() == "comum" else -5000
        #Para verificar se o saldo é compativel com o valor solicitado para depósito
        if valor_cliente - valor_a_debitar >= limite_negativo:
          valor_cliente -= valor_a_debitar
          print(f"Valor disponível: {valor_cliente}")
          print(f"Valor debitado com sucesso e taxa de {taxa*100}% aplicada.")

          # Atualizar apenas a linha "Valor" no arquivo
          dados_arquivo[indice_cnpj + 2] = f"Valor: {valor_cliente}\n"

          with open("clientes.txt", "w") as arquivo:
            arquivo.writelines(dados_arquivo)
          # Abertura do arquivo para que ocorra a alteração  de saldo nos clientes.txt e adicione as informações de operação no arquivo operacoes.txt
          with open(f"{cnpj3}_operacoes.txt", "a") as arquivo_operacoes:
            arquivo_operacoes.write(
                f"Débito de {valor2} realizado. Nova taxa: {taxa*100}%\n")
            arquivo_operacoes.write(f"Saldo após débito: {valor_cliente}\n\n")
        else:
          print("Saldo insuficiente para debitar.")
      else:
        print("Senha Inválida")
    else:
      print("Informações incompletas no arquivo.")
  else:
    print("CNPJ  não encontrado.")


#xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxFUNÇÃO-05: DEPÓSITOxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


def deposito(cnpj3, deposito_conta):
  for cliente in lista_cliente:
      if cliente["CNPJ"] == cnpj3:
          cliente["Valor"] += deposito_conta
          print(f"Valor depositado com sucesso: {deposito_conta}")

          # Toda vez que uma operação for concluída, alterar informações no arquivo de operações.
          with open(f"{cliente['CNPJ']}_operacoes.txt", "a") as arquivo_operacoes:
              arquivo_operacoes.write(f"Depósito de {deposito_conta} realizado.\n")
              arquivo_operacoes.write(f"Saldo após depósito: {cliente['Valor']}\n\n")

          break  # Mova o break para dentro do bloco if
  else:
      print("CNPJ não encontrado.")

  with open("clientes.txt", "w") as arquivo:
      arquivo.writelines(
          f"Nome: {cliente['Nome']}\nCNPJ: {cliente['CNPJ']}\nConta: {cliente['Conta']}\nValor: {cliente['Valor']}\nSenha: {cliente['Senha']}\n\n"
          for cliente in lista_cliente)

#xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxFUNÇÃO-06: EXTRATOxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


def extrato(lista_cliente, cnpj, senha):
  # Para percorrer a lista para a verifição das informações recebidas com as informações presentes no cadastro
  for cliente in lista_cliente:
    if cliente["CNPJ"] == cnpj and cliente["Senha"] == senha:
      print("Extrato de Operações:")
      operacoes = []
      # Abertura do arquivo para a leitura das informações presentes nele
      try:
        with open(f"{cliente['CNPJ']}_operacoes.txt", "r") as arquivo:
          operacoes = arquivo.read()
        print(operacoes)
      except FileNotFoundError:
        print("Nenhuma operação registrada.")
      break


#xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxFUNÇÃO-07: TRANSFERENCIAxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


def transferencia(lista_cliente, cnpj_origem, senha3, cnpj_destino, valor3):
  # Caso não seja encontrado o cliente de origem e destino
  cliente_origem = None
  cliente_destino = None
  # Abertura do Arquivo para a leitura das informações
  with open("clientes.txt", "r") as arquivo:
    dados_arquivo = arquivo.readlines()

  # Para percorrer a lista e numerar o indice especifico onde esta o cnpj de origem
  indice_cnpj_origem = -1
  for i, linha in enumerate(dados_arquivo):
    if linha.startswith(f"CNPJ: {cnpj_origem}"):
      indice_cnpj_origem = i
      break
  

  # Para percorrer a lista e numerar o indice especifico onde esta o cnpj de destino
  indice_cnpj_destino = -1
  for i, linha in enumerate(dados_arquivo):
    if linha.startswith(f"CNPJ: {cnpj_destino}"):
      indice_cnpj_destino = i
      break

  # Caso não seja encontrado a senha, o valor e o tipo da conta
  senha_arquivo_origem = None
  valor_cliente_origem = None
  tipo_conta_origem = None

  # Para percorrer a linha e numerar o indice especifico onde esta a senha, o valor e o tipo da conta daquele cnpj
  for j in range(indice_cnpj_origem,
                 min(indice_cnpj_origem + 5, len(dados_arquivo))):
    if "Senha" in dados_arquivo[j]:
      senha_arquivo_origem = int(dados_arquivo[j].split(":")[1].strip())
    elif "Valor" in dados_arquivo[j]:
      valor_cliente_origem = float(dados_arquivo[j].split(":")[1].replace(
          ",", ".").strip())
    elif "Conta" in dados_arquivo[j]:
      tipo_conta_origem = dados_arquivo[j].split(":")[1].strip()

  # Caso não seja encontrado o valor do cliente de destino
  valor_cliente_destino = None

  # Para percorrer a linha e numerar o indice especifico onde esta o valor
  for j in range(indice_cnpj_destino,
                 min(indice_cnpj_destino + 5, len(dados_arquivo))):
    if "Valor" in dados_arquivo[j]:
      valor_cliente_destino = float(dados_arquivo[j].split(":")[1].replace(
          ",", ".").strip())

  # Verificação da senha e do valor aplicando as propriedades de limite para o tipo de conta
  if senha_arquivo_origem is not None and valor_cliente_origem is not None and tipo_conta_origem is not None:
    if senha_arquivo_origem == senha3:
      valor_a_transferir = valor3
      limite_negativo = -1000 if tipo_conta_origem.lower(
      ) == "comum" else -5000
      if valor_cliente_origem - valor_a_transferir >= limite_negativo:
        valor_cliente_origem -= valor_a_transferir
        valor_cliente_destino += valor_a_transferir

        # Atualizar valores no arquivo
        dados_arquivo[indice_cnpj_origem +
                      2] = f"Valor: {valor_cliente_origem}\n"
        dados_arquivo[indice_cnpj_destino +
                      2] = f"Valor: {valor_cliente_destino}\n"

        # Atualizar arquivos de operações
        with open("clientes.txt", "w") as arquivo:
          arquivo.writelines(dados_arquivo)

        with open(f"{cnpj_origem}_operacoes.txt",
                  "a") as arquivo_operacoes_origem:
          arquivo_operacoes_origem.write(
              f"Transferência de {valor3} para CNPJ {cnpj_destino} realizado.\n"
          )
          arquivo_operacoes_origem.write(
              f"Saldo após transferência: {valor_cliente_origem}\n\n")

        with open(f"{cnpj_destino}_operacoes.txt",
                  "a") as arquivo_operacoes_destino:
          arquivo_operacoes_destino.write(
              f"Recebimento de transferência de CNPJ {cnpj_origem}. Valor: {valor3}\n"
          )
          arquivo_operacoes_destino.write(
              f"Saldo após recebimento: {valor_cliente_destino}\n\n")
        print("Transferência realizada com sucesso.")
      # Elses para caso não possua saldo, ou a senha esteja inválida ou falte alguma informação
      else:
        print("Saldo insuficiente para a transferência.")
    else:
      print("Senha Inválida")
  else:
    print("Informações incompletas no arquivo.")

    with open("clientes.txt", "w") as arquivo:
      arquivo.writelines(dados_arquivo)


#xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxFUNÇÃO-08: LIVRExxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


def livre(lista_cliente, cnpj3, senha5, nome_cliente, cpf_cliente):
  # Para percorrer a lista e fazer a verificação para confirmar o cnpj e a senha correspondem
  for cliente in lista_cliente:
    if cliente["CNPJ"] == cnpj3:
      if cliente["Senha"] == senha5:
        print(nome_cliente)
        print(cpf_cliente)

        # Abertura do arquivo onde sera armazenados os clientes de cada cnpj
        with open(f"{cliente['CNPJ']}_clientes.txt", "a") as arquivo_clientes:
          arquivo_clientes.write(
              f"Nome: {nome_cliente}\nCPF: {cpf_cliente}\n\n")
        print("Cadastro realizado com sucesso.")
      else:
        print("Senha Inválida")

  #abertura de arquivo para confirmar as informações do cnpj e senha
  with open("clientes.txt", "w") as arquivo:
    arquivo.writelines(
        f"Nome: {cliente['Nome']}\nCNPJ: {cliente['CNPJ']}\nConta: {cliente['Conta']}\nValor: {cliente['Valor']}\nSenha: {cliente['Senha']}\n\n"
        for cliente in lista_cliente)


#xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxMENU INCIALxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


# Começo de um looping para que as opções de ações apareçam enquanto não for solicitado a função 9
while True:
  print("")
  print("1: Login / Novo Cadastro")
  print("2: Excluir Cadastro")
  print("3: Listar Clientes")
  print("4: Débito")
  print("5: Deposito")
  print("6: Extrato")
  print("7: Transferência")
  print("8: Cadastro de Clientes")
  print("9: Sair")
  print("")
  menu = int(input("Digite a opção desejada: "))
  if menu == 9:
    break

# Opções do Menu Inicial e Solicitação de informações
  elif menu == 1:
    clientes(lista_cliente)

  elif menu == 2:
    apagar_cliente(lista_cliente)

  elif menu == 3:
    listar()

  elif menu == 4:
    cnpj3 = input("Digite Seu CNPJ: ")
    senha2 = int(input("Digite Sua Senha: "))
    valor2 = float(input("Digite o Valor: "))
    debito(lista_cliente, cnpj3, senha2, valor2)

  elif menu == 5:
    cnpj3 = input("Digite Seu CNPJ: ")
    deposito_conta = float(input("Digite o Valor Que Deseja Depositar: "))
    deposito(cnpj3, deposito_conta)

  elif menu == 6:
    cnpj = input("Digite Seu CNPJ: ")
    senha = int(input("Digite Sua Senha: "))
    extrato(lista_cliente, cnpj, senha)

  elif menu == 7:
    cnpj_origem = input("Digite Seu CNPJ: ")
    senha = int(input("Digite Sua Senha: "))
    cnpj_destino = input("Digite CNPJ de Destino: ")
    valor = float(input("Digite o Valor que Deseja Transferir: "))
    transferencia(lista_cliente, cnpj_origem, senha, cnpj_destino, valor)

  elif menu == 8:
    cnpj3 = input("Digite o seu CNPJ: ")
    senha5 = int(input("Digite sua Senha: "))
    nome_cliente = input("Digite o nome do Cliente: ")
    cpf_cliente = input("Digite o CPF do Cliente: ")
    livre(lista_cliente, cnpj3, senha5, nome_cliente, cpf_cliente)
#FIM!
