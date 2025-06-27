# Esse RPA serve para automatizar porcessos de notas fiscais para a empresa do meu pai. Ele está em processo inical, por isso tem partes flatando e não está 100% polido.


from datetime import datetime, timedelta

VALIDADES = {
    "Varal de Teto": 5 * 365,
    "Rede de Proteção": 3 * 365,
    "Tela Mosquiteiro": 10 * 365,
}

notas = []

# Pede a data de verificação no início
while True:
    data_verificacao_str = input("Digite a data de verificação (dd/mm/aaaa): ")
    try:
        data_verificacao = datetime.strptime(data_verificacao_str, "%d/%m/%Y")
        break
    except:
        print("Data inválida. Tente novamente.")

def registrar_nota():
    print("\n--- Registrar nova nota ---")
    tipo = input("Tipo do serviço (Varal de Teto / Rede de Proteção / Tela Mosquiteiro): ")

    if tipo not in VALIDADES:
        print("Tipo inválido.")
        return

    data_str = input("Data do serviço (dd/mm/aaaa): ")
    try:
        data_servico = datetime.strptime(data_str, "%d/%m/%Y")
    except:
        print("Data inválida.")
        return

    valor = input("Valor do serviço: ")
    numero_nota = input("Número da nota fiscal: ")

    validade = data_servico + timedelta(days=VALIDADES[tipo])

    detalhes = {}

    if tipo == "Rede de Proteção":
        detalhes["Janelas"] = input("Quantidade de janelas: ")
        detalhes["Varandas"] = input("Quantidade de varandas: ")
        detalhes["Basculantes"] = input("Quantidade de basculantes: ")

    elif tipo == "Varal de Teto":
        modelo = input("Modelo (5, 10, 15 varetas ou personalizado): ")
        tamanho = input("Tamanho da vareta (1m, 1.20m, 1.50m ou personalizado): ")
        detalhes["Modelo"] = modelo
        detalhes["Tamanho da vareta"] = tamanho

    elif tipo == "Tela Mosquiteiro":
        detalhes["Janelas"] = input("Quantidade de janelas com tela: ")

    nota = {
        "tipo": tipo,
        "data_servico": data_servico,
        "valor": valor,
        "numero_nota": numero_nota,
        "data_validade": validade,
        "detalhes": detalhes
    }

    notas.append(nota)
    print("Nota registrada com sucesso.")

def listar_todas():
    if not notas:
        print("\nNenhuma nota registrada.")
        return

    print("\n--- Todas as Notas Registradas (ordenadas pelo número da nota) ---")

    # Ordena pela numeração da nota (convertendo para int)
    try:
        notas_ordenadas = sorted(notas, key=lambda x: int(x['numero_nota']))
    except ValueError:
        # Se número da nota não for inteiro, ordena alfabeticamente
        notas_ordenadas = sorted(notas, key=lambda x: x['numero_nota'])

    for i, nota in enumerate(notas_ordenadas):
        print(f"\nNota {i+1}")
        print(f"Tipo: {nota['tipo']}")
        print(f"Data do serviço: {nota['data_servico'].strftime('%d/%m/%Y')}")
        print(f"Valor: R${nota['valor']}")
        print(f"Número da nota: {nota['numero_nota']}")
        print(f"Validade: {nota['data_validade'].strftime('%d/%m/%Y')}")
        print("Detalhes:")
        for chave, valor in nota["detalhes"].items():
            print(f"  - {chave}: {valor}")

def verificar_atrasos():
    if not notas:
        print("\nNenhuma nota registrada.")
        return

    print(f"\n--- Verificação de serviços em atraso na data: {data_verificacao.strftime('%d/%m/%Y')} ---")
    atrasos_encontrados = False

    for nota in notas:
        dias_faltando = (nota["data_validade"] - data_verificacao).days

        if dias_faltando < 0:
            atrasos_encontrados = True
            print(f"\n{nota['tipo']} - Nota {nota['numero_nota']} - Valor: R${nota['valor']}")
            print(f"Serviço em: {nota['data_servico'].strftime('%d/%m/%Y')}")
            print(f"Vencimento: {nota['data_validade'].strftime('%d/%m/%Y')}")
            print("Detalhes:")
            for chave, valor in nota["detalhes"].items():
                print(f"  - {chave}: {valor}")
            print(f"⚠️ SERVIÇO EM ATRASO ({abs(dias_faltando)} dias)")

    if not atrasos_encontrados:
        print("\nNenhum serviço está em atraso.")

# Loop principal
while True:
    print("BEM VINDO AO SISTEMA AUTOMATIZADO DE NFs DA AMB TELAS!")
    print("\n=== MENU ===")
    print("1 - Registrar nova nota")
    print("2 - Verificar serviços em atraso")
    print("3 - Listar todas as notas")
    print("4 - Sair")

    opcao = input("Escolha uma opção: ")

    if opcao == "1":
        registrar_nota()
    elif opcao == "2":
        verificar_atrasos()
    elif opcao == "3":
        listar_todas()
    elif opcao == "4":
        print("Encerrando o programa.")
        break
    else:
        print("Opção inválida.")
