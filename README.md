# gerenciamento.py
Projeto de gerenciamento de Hotel usando Python.

  from datetime import datetime, date
  from typing import List, Optional


  class Quarto:
      def __init__(self, numero: int, tipo: str, preco_diaria: float):
          self.numero = numero
          self.tipo = tipo  # "Solteiro", "Casal", "Familia"
          self.preco_diaria = preco_diaria
          self.ocupado = False

      def __str__(self):
          status = "ocupado" if self.ocupado else "disponível"
          return f"Quarto {self.numero} - {self.tipo} - R${self.preco_diaria:.2f}/dia - {status}"


  class Hospede:
      def __init__(self, nome: str, cpf: str, telefone: str, email: str):
          self.nome = nome
          self.cpf = cpf
          self.telefone = telefone
          self.email = email

      def __str__(self):
          return f"{self.nome} - CPF: {self.cpf} - Tel: {self.telefone}"


  class Reserva:
      def __init__(self, hospede: Hospede, quarto: Quarto, data_entrada: date, data_saida: date):
          self.hospede = hospede
          self.quarto = quarto
          self.data_entrada = data_entrada
          self.data_saida = data_saida
          self.valor_total = self.calcular_valor_total()

      def calcular_valor_total(self) -> float:
          dias = (self.data_saida - self.data_entrada).days
          return dias * self.quarto.preco_diaria

      def __str__(self):
          return (f"Reserva: {self.hospede.nome} - Quarto {self.quarto.numero} - "
                  f"{self.data_entrada} a {self.data_saida} - R${self.valor_total:.2f}")


  class Hotel:
      def __init__(self, nome: str):
          self.nome = nome
          self.quartos: List[Quarto] = []
          self.hospedes: List[Hospede] = []
          self.reservas: List[Reserva] = []

      def adicionar_quarto(self, numero: int, tipo: str, preco_diaria: float):
          quarto_obj = Quarto(numero, tipo, preco_diaria)
          self.quartos.append(quarto_obj)
          print(f"Quarto {numero} adicionado com sucesso!")

      def cadastrar_hospede(self, nome: str, cpf: str, telefone: str, email: str):
          hospede_obj = Hospede(nome, cpf, telefone, email)
          self.hospedes.append(hospede_obj)
          print(f"Hóspede {nome} cadastrado com sucesso!")
          return hospede_obj

      def buscar_quarto(self, numero: int) -> Optional[Quarto]:
          for quarto in self.quartos:
              if quarto.numero == numero:
                  return quarto
          return None

      def buscar_hospede(self, cpf: str) -> Optional[Hospede]:
          for hospede in self.hospedes:
              if hospede.cpf == cpf:
                  return hospede
          return None

      def quartos_disponiveis(self) -> List[Quarto]:
          return [quarto for quarto in self.quartos if not quarto.ocupado]

      def fazer_reserva(self, cpf_hospede: str, numero_quarto: int, data_entrada: date, data_saida: date):
          hospede = self.buscar_hospede(cpf_hospede)
          quarto = self.buscar_quarto(numero_quarto)

          if not hospede:
              print("Hóspede não encontrado!")
              return False

          if not quarto:
              print("Quarto não encontrado!")
              return False

          if quarto.ocupado:
              print("Quarto já está ocupado!")
              return False

          reserva_obj = Reserva(hospede, quarto, data_entrada, data_saida)
          self.reservas.append(reserva_obj)
          quarto.ocupado = True
          print(f"Reserva feita com sucesso! Valor total: R${reserva_obj.valor_total:.2f}")
          return True

      def check_out(self, numero_quarto: int):
          quarto = self.buscar_quarto(numero_quarto)
          if quarto and quarto.ocupado:
              quarto.ocupado = False
              print(f"Check-out realizado para o quarto {numero_quarto}")
          else:
              print("Quarto não encontrado ou já está disponível")

      def listar_quartos(self):
          print(f"\n=== Quartos do {self.nome} ===")
          for quarto in self.quartos:
              print(quarto)

      def listar_hospedes(self):
          print(f"\n=== Hóspedes cadastrados ===")
          for hospede in self.hospedes:
              print(hospede)

      def listar_reservas(self):
          print(f"\n=== Reservas ativas ===")
          for reserva in self.reservas:
              print(reserva)


  def menu_principal():
      hotel = Hotel("Hotel Python")

      # Adicionando alguns quartos de exemplo
      hotel.adicionar_quarto(100, "Solteiro", 100.00)
      hotel.adicionar_quarto(101, "Casal", 150.00)
      hotel.adicionar_quarto(102, "Familia", 200.00)

      while True:
          print(f"\n=== Sistema de Gerenciamento - {hotel.nome} ===")
          print("1. Listar quartos")
          print("2. Cadastrar hóspede")
          print("3. Fazer reserva")
          print("4. Check-out")
          print("5. Listar hóspedes")
          print("6. Listar reservas")
          print("7. Listar quartos disponíveis")
          print("0. Sair")

          opcao = input("\nEscolha uma opção: ")

          if opcao == "1":
              hotel.listar_quartos()

          elif opcao == "2":
              nome = input("Nome do hóspede: ")
              cpf = input("CPF: ")
              telefone = input("Telefone: ")
              email = input("Email: ")
              hotel.cadastrar_hospede(nome, cpf, telefone, email)

          elif opcao == "3":
              cpf = input("CPF do hóspede: ")
              numero_quarto = int(input("Número do quarto: "))
              data_entrada_str = input("Data de entrada (AAAA-MM-DD): ")
              data_saida_str = input("Data de saída (AAAA-MM-DD): ")

              try:
                  data_entrada = datetime.strptime(data_entrada_str, "%Y-%m-%d").date()
                  data_saida = datetime.strptime(data_saida_str, "%Y-%m-%d").date()
                  hotel.fazer_reserva(cpf, numero_quarto, data_entrada, data_saida)
              except ValueError:
                  print("Formato de data inválido!")

          elif opcao == "4":
              numero_quarto = int(input("Número do quarto para o check-out: "))
              hotel.check_out(numero_quarto)

          elif opcao == "5":
              hotel.listar_hospedes()

          elif opcao == "6":
              hotel.listar_reservas()

          elif opcao == "7":
              print("\n=== Quartos disponíveis ===")
              for quarto in hotel.quartos_disponiveis():
                  print(quarto)

          elif opcao == "0":
              print("Obrigado por se hospedar no Hotel Python!")
              break

          else:
              print("Opção inválida!")


  if __name__ == "__main__":
      menu_principal()

