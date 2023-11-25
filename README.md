# Proyecto-Final-

https://replit.com/@andresrestrepog/Proyecto-Final-Lenguajes-de-programacion

https://youtu.be/GPm1UVaRaKM


import random


class Nodo:

  def __init__(self, participante):
    self.participante = participante
    self.siguiente = None


class ListaEnlazada:

  def __init__(self):
    self.inicio = None

  def agregar_participante(self, participante):
    nuevo_nodo = Nodo(participante)
    nuevo_nodo.siguiente = self.inicio
    self.inicio = nuevo_nodo

  def imprimir_participantes(self):
    actual = self.inicio
    while actual:
      participante = actual.participante
      print(
          f"\nNombre: {participante.nombre}, Teléfono: {participante.telefono}"
      )
      print(
          f"Progreso: {participante.progreso}/15, Última pregunta: {participante.ultima_pregunta_correcta}"
      )
      print(
          f"Valor premio: ${participante.valor_premio}, Comodines: {participante.comodines}"
      )
      actual = actual.siguiente


class Pregunta:

  def __init__(self, enunciado, opciones, respuesta_correcta, seguro=False):
    self.enunciado = enunciado
    self.opciones = opciones
    self.respuesta_correcta = respuesta_correcta
    self.seguro = seguro


class Concursante:

  def __init__(self, nombre, telefono):
    self.nombre = nombre
    self.telefono = telefono
    self.progreso = 0
    self.valor_premio = 0
    self.ultima_pregunta_correcta = 0
    self.comodines = {
        "50/50": 1,
        "ayuda_del_publico": 1,
        "llamada_a_un_amigo": 1
    }

  def responder_pregunta(self, pregunta, respuesta, programa_concurso):
    if respuesta == pregunta.respuesta_correcta:
      self.progreso += 1
      self.valor_premio = pregunta.seguro if pregunta.seguro else self.obtener_valor_premio(
      )
      self.ultima_pregunta_correcta = self.progreso
      return True, respuesta  # Return True to indicate a correct answer
    else:
      print("Respuesta incorrecta. Se ofrecen tres ayudas:")
      print("1. 50/50")
      print("2. Ayuda del público")
      print("3. Llamar a un amigo")

      opcion_ayuda = input(
          "Ingrese el número de la ayuda que desea utilizar: ")

      if opcion_ayuda == "1" and self.comodines["50/50"] > 0:
        respuesta = programa_concurso.usar_comodin_50_50(pregunta, self)
      elif opcion_ayuda == "2" and self.comodines["ayuda_del_publico"] > 0:
        programa_concurso.usar_comodin_ayuda_del_publico(pregunta)
        respuesta = input("Ingrese la letra de su respuesta: ").upper()
      elif opcion_ayuda == "3" and self.comodines["llamada_a_un_amigo"] > 0:
        programa_concurso.usar_comodin_llamada_a_un_amigo(pregunta)
        respuesta = input("Ingrese la letra de su respuesta: ").upper()

      if self.comodines["50/50"] == 0 and self.comodines[
          "ayuda_del_publico"] == 0 and self.comodines[
              "llamada_a_un_amigo"] == 0:
        print("Se acabaron las ayudas. Fin del juego.")
        return False, respuesta  # Return False to indicate an incorrect answer and end the game
      else:
        return False, respuesta

  def obtener_valor_premio(self):
    valores_premios = [
        100000, 200000, 300000, 500000, 1000000, 2000000, 3000000, 5000000,
        7000000, 10000000, 12000000, 20000000, 50000000, 100000000, 300000000
    ]
    return valores_premios[self.progreso - 1]


class ProgramaConcurso:

  def __init__(self):
    self.participantes = ListaEnlazada()
    self.valor_total_emisiones = 0

  def ejecutar_concurso(self):
    for _ in range(2):  # Dos participantes por emisión
      participante = self.nuevo_participante()
      if not participante:
        break

      self.participantes.agregar_participante(participante)
      self.valor_total_emisiones += self.realizar_concurso(participante)

    self.mostrar_reporte()

  def nuevo_participante(self):
    nombre = input("Ingrese el nombre del participante: ")
    telefono = input("Ingrese el número de teléfono del participante: ")
    return Concursante(nombre, telefono)

  def realizar_concurso(self, participante):
    print(f"\n¡Bienvenido {participante.nombre} al concurso!")

    while participante.progreso < 15:
      pregunta_actual = self.generar_pregunta(participante.progreso + 1)
      print(
          f"\nPregunta {participante.progreso + 1}: {pregunta_actual.enunciado}"
      )
      self.mostrar_opciones(pregunta_actual.opciones)

      respuesta = input("Ingrese la letra de su respuesta: ").upper()

      if respuesta == "50/50" and participante.comodines["50/50"] > 0:
        respuesta = self.usar_comodin_50_50(pregunta_actual, participante)
      elif respuesta == "AYUDA_DEL_PUBLICO" and participante.comodines[
          "ayuda_del_publico"] > 0:
        self.usar_comodin_ayuda_del_publico(pregunta_actual)
      elif respuesta == "LLAMADA_A_UN_AMIGO" and participante.comodines[
          "llamada_a_un_amigo"] > 0:
        self.usar_comodin_llamada_a_un_amigo(pregunta_actual)
      else:
        respuesta_correcta, respuesta = participante.responder_pregunta(
            pregunta_actual, respuesta, self)
        if respuesta_correcta:
          print("¡Respuesta correcta!")
        else:
          break  # Exit the loop when the game ends due to an incorrect answer with no remaining lifelines

    if participante.progreso == 15:
      print(
          f"\n¡Felicidades, {participante.nombre}! Has respondido todas las preguntas correctamente."
      )
    else:
      print(
          f"\n{participante.nombre} ha finalizado el juego. Premio obtenido: ${participante.valor_premio}"
      )

    return participante.valor_premio

  def generar_pregunta(self, numero_pregunta):
    # Implementa la lógica para generar preguntas según el número de pregunta
    # Puedes adaptar esta parte según tus necesidades
    enunciado = f"Pregunta {numero_pregunta}: ¿Enunciado de la pregunta?"
    opciones = ["A", "B", "C", "D"]
    respuesta_correcta = random.choice(opciones)
    seguro = numero_pregunta in [5, 10, 15]
    return Pregunta(enunciado, opciones, respuesta_correcta, seguro)

  def mostrar_opciones(self, opciones):
    for opcion in opciones:
      print(opcion)

  def usar_comodin_50_50(self, pregunta, participante):
    opciones_posibles = [
        opcion for opcion in pregunta.opciones
        if opcion != pregunta.respuesta_correcta
    ]
    opciones_eliminar = random.sample(opciones_posibles, 2)

    # Mostrar la pregunta con las opciones eliminadas
    print(f"\nPregunta {participante.progreso + 1}: {pregunta.enunciado}")
    for opcion in pregunta.opciones:
      if opcion == pregunta.respuesta_correcta or opcion not in opciones_eliminar:
        print(opcion)

    # Permitir al participante ingresar la respuesta
    respuesta = input("Ingrese la letra de su respuesta: ").upper()

    # Validar que la respuesta sea válida
    while respuesta not in pregunta.opciones or respuesta == "X":
      print("Respuesta no válida. Ingrese una opción válida.")
      respuesta = input("Ingrese la letra de su respuesta: ").upper()

    participante.comodines["50/50"] -= 1
    print(
        "Comodín 50/50 utilizado. Las opciones incorrectas han sido eliminadas."
    )

    return respuesta

  def usar_comodin_ayuda_del_publico(self, pregunta):
    # Implementa la lógica para simular la ayuda del público
    print("Comodín ayuda del público utilizado. Respuestas del público...")

  def usar_comodin_llamada_a_un_amigo(self, pregunta):
    # Implementa la lógica para simular la llamada a un amigo
    print("Comodín llamada a un amigo utilizado. Respuesta del amigo...")

  def mostrar_reporte(self):
    print("\nReporte Final:")
    self.participantes.imprimir_participantes()
    print(
        f"\nValor total otorgado en la semana: ${self.valor_total_emisiones}")


# Ejemplo de uso
programa_concurso = ProgramaConcurso()
programa_concurso.ejecutar_concurso()
