# Programa-tarea-unidad-6
codigo de la tarea unidad 6
import os
import pickle

TXT_FILE = "peliculas.txt"
BIN_FILE = "peliculas.bin"

def crear_archivo_texto():
    if not os.path.exists(TXT_FILE):
        with open(TXT_FILE, "w", encoding="utf-8") as f:
            f.write("=== COLECCION DE PELICULAS ===\n")
        print("El archivo de texto se creo correctamente.")


def agregar_pelicula_texto(nombre, genero, año, director):
    try:
        with open(TXT_FILE, "a", encoding="utf-8") as f:
            f.write(f"{nombre}|{genero}|{año}|{director}\n")
    except Exception as e:
        print("Error al escribir en el archivo de texto:", e)


def mostrar_peliculas_texto():
    try:
        with open(TXT_FILE, "r", encoding="utf-8") as f:
            contenido = f.readlines()
            if len(contenido) <= 1:
                print("No hay ninguna pelicula registrada.")
                return

            for linea in contenido[1:]:
                nombre, genero, año, director = linea.strip().split("|")
                print(f" {nombre} | Genero: {genero} | Año: {año} | Director: {director}")

    except FileNotFoundError:
        print("No existe el archivo de texto.")
    except Exception as e:
        print("Hubo un problema al leer el archivo:", e)


def buscar_pelicula(nombre_buscado):
    try:
        with open(TXT_FILE, "r", encoding="utf-8") as f:
            for linea in f:
                if linea.startswith("==="):
                    continue

                nombre, genero, año, director = linea.strip().split("|")
                if nombre.lower() == nombre_buscado.lower():
                    return (nombre, genero, año, director)
        return None

    except FileNotFoundError:
        print("No se encontro ningun archivo.")
    except Exception as e:
        print("Error al buscar:", e)
    
    return None


def guardar_datos_binarios(nombre, popularidad):
    try:
        if popularidad < 0 or popularidad > 100:
            raise ValueError("La popularidad debe estar entre 0 y 100.")

        datos = {}

        if os.path.exists(BIN_FILE):
            with open(BIN_FILE, "rb") as bf:
                datos = pickle.load(bf)

        datos[nombre] = popularidad

        with open(BIN_FILE, "wb") as bf:
            pickle.dump(datos, bf)

    except ValueError as ve:
        print("Error:", ve)
    except Exception as e:
        print("Error al guardar los datos binarios:", e)
    finally:
        print("Proceso binario finalizado.")


def mostrar_datos_binarios():
    try:
        with open(BIN_FILE, "rb") as bf:
            datos = pickle.load(bf)

        print("=== Datos Binarios (Popularidad) ===")
        for nombre, popularidad in datos.items():
            print(f" {nombre}: Popularidad {popularidad}/100")

    except FileNotFoundError:
        print("El archivo binario no existe.")
    except Exception as e:
        print("Error al leer archivo binario:", e)
    finally:
        print("Lectura binaria finalizada.")


def menu():
    crear_archivo_texto()

    while True:
        print("\n===== MI COLECCION DIGITAL: CATALOGO DE PELICULAS =====")
        print("1. Agregar pelicula")
        print("2. Mostrar coleccion completa")
        print("3. Buscar una pelicula por su nombre")
        print("4. Mostrar datos binarios (popularidad)")
        print("5. Salir")

        opcion = input("Selecciona una opcion: ")

        if opcion == "1":
            nombre = input("Nombre de la pelicula: ").strip()
            genero = input("Genero: ").strip()
            año = input("Año: ").strip()
            director = input("Director: ").strip()

            if not nombre:
                print("El nombre no puede estar vacio.")
                continue

            pop = input("Popularidad (0-100): ")

            try:
                pop = int(pop)
            except:
                print("Error: La popularidad debe de ser un numero.")
                continue

            agregar_pelicula_texto(nombre, genero, año, director)
            guardar_datos_binarios(nombre, pop)
            print("Pelicula agregada con exito.")

        elif opcion == "2":
            mostrar_peliculas_texto()

        elif opcion == "3":
            nombre = input("Ingresa el nombre de la pelicula a buscar: ")
            resultado = buscar_pelicula(nombre)

            if resultado:
                n, g, a, d = resultado
                print(f"Encontrado:  {n} | {g} | {a} | {d}")
            else:
                print("Pelicula no encontrada.")

        elif opcion == "4":
            mostrar_datos_binarios()

        elif opcion == "5":
            print("Saliendo del programa...")
            break

        else:
            print("Opcion no valida.")


if __name__ == "__main__":
    menu()
