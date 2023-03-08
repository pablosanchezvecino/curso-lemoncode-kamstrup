# Laboratorio 01 - Sintaxis

## Ejercicio 1
``` C#
using System;

namespace lab1
{
    internal class Program
    {
        static void Main(string[] args)
        {
            while (true)
            {
                ProcesarEntrada(out string nombre, out uint edad, out string ciudad);
                Console.WriteLine($"Te llamas {nombre} y tienes {edad} años. Bienvenido a {ciudad}");
            }
        }

        private static void ProcesarEntrada(out string nombre, out uint edad, out string ciudad)
        {
            bool nombreValido;
            bool edadValida;
            bool ciudadValida;

            do
            {
                Console.WriteLine("Introduzca su nombre:");
                nombre = Console.ReadLine();
                nombreValido = (nombre is not null) && (!nombre.Equals(""));

                if (!nombreValido)
                {
                    Console.WriteLine("Edad no válida");
                }

            } while (!nombreValido);

            do
            {
                Console.WriteLine("Introduzca su edad:");
                edadValida = uint.TryParse(Console.ReadLine(), out edad);

                if (!edadValida)
                {
                    Console.WriteLine("Edad no válida");
                }

            } while (!edadValida);

            do
            {
                Console.WriteLine("Introduza el nombre de una ciudad:");
                ciudad = Console.ReadLine();
                ciudadValida = (ciudad is not null) && (!ciudad.Equals(""));

                if (!ciudadValida)
                {
                    Console.WriteLine("Ciudad no válida");
                }

            } while (!ciudadValida);
        }
    }
}

```

## Ejercicio 2
``` C#
using System;

namespace lab1
{
    internal class Program
    {
        static void Main(string[] args)
        {
            while (true)
            {
                ProcesarEntrada(out int num1, out int num2);
                Comparar(num1, num2);
            }
        }

        private static void ProcesarEntrada(out int num1, out int num2)
        {
            bool num1Valido;
            bool num2Valido;

            do
            {
                Console.WriteLine("Introduzca el primer número:");
                num1Valido = int.TryParse(Console.ReadLine(), out num1);

                if (!num1Valido)
                {
                    Console.WriteLine("Entrada no válida");
                }

            } while (!num1Valido);

            do
            {
                Console.WriteLine("Introduzca el segundo número:");
                num2Valido = int.TryParse(Console.ReadLine(), out num2);

                if (!num2Valido)
                {
                    Console.WriteLine("Entrada no válida");
                }

            } while (!num2Valido);
        }

        private static void Comparar(int num1, int num2)
        {
            if (num1 > num2)
            {
                Console.WriteLine("El primer número es mayor que el segundo");
            }
            else if (num2 > num1)
            {
                Console.WriteLine("El segundo número es mayor que el primero");
            }
            else
            {
                Console.WriteLine("Los dos números son iguales");
            }
        }
    }
}

```

## Ejercicio 3
``` C#
using System;

namespace lab1
{
    internal class Program
    {
        public enum DiasSemana
        {
            lunes,
            martes,
            miercoles,
            jueves,
            viernes,
            sabado,
            domingo
        }

        static void Main(string[] args)
        {
            DiasSemana dia;
            bool diaValido;

            while (true)
            {
                do
                {
                    Console.WriteLine("Introduzca un día de la semana (sin tildes):");
                    diaValido = Enum.TryParse(Console.ReadLine().ToLower(), out dia);

                    if (!diaValido)
                    {
                        Console.WriteLine("Entrada no válida");
                    }

                } while (!diaValido);

                Console.WriteLine(((int)dia) > 4 ? "Es fin de semana" : "No es fin de semana");
            }
        }
    }
}

```

## Ejercicio 4
``` C#
using System;

namespace pruebas2
{
    internal class Program
    {
        static void Main(string[] args)
        {
            for (int i = 1; i <= 100; i++)
            {
                if (i % 2 == 0)
                {
                    Console.WriteLine(i);
                }
            }
        }
    }
}

```

## Ejercicio 5
``` C#
using System;

namespace pruebas2
{
    internal class Program
    {
        static void Main(string[] args)
        {
            while (true)
            {
                uint cantidad = ProcesarEntrada();
                int[] randoms = GenerarArrayAleatorio(cantidad);
                MostrarArray(randoms);
            }
        }

        private static uint ProcesarEntrada()
        {
            uint cantidad;
            bool cantidadValida;
            do
            {
                Console.WriteLine("Introduzca la cantidad de números aleatorios a generar:");
                cantidadValida = uint.TryParse(Console.ReadLine(), out cantidad);

                if (!cantidadValida)
                {
                    Console.WriteLine("Cantidad no válida");
                }

            } while (!cantidadValida);

            return cantidad;
        }

        private static int[] GenerarArrayAleatorio(uint cantidad)
        {
            int[] randoms = new int[cantidad];
            Random rnd = new Random();

            for (int i = 0; i < cantidad; i++)
            {
                randoms[i] = rnd.Next();
            }

            return randoms;
        }

        private static void MostrarArray(int[] array)
        {
            Console.Write("Array de números aleatorios generado: ");
            Console.WriteLine("{0}", string.Join(", ", array));
        }
    }
}

```

## Ejercicio 6
``` C#
using System;

namespace lab1
{
    internal class Program
    {
        static void Main(string[] args)
        {
            int filas;
            char caracter;

            while (true)
            {
                ProcesarEntrada(out filas, out caracter);
                GenerarPiramide(filas, caracter);
            }
        }

        private static void GenerarPiramide(int filas, char caracter)
        {
            Console.WriteLine("Generando pirámide...");

            // Primera fila
            Console.WriteLine(caracter);

            // Filas de en medio
            for (int i = 0; i < filas - 2; i++)
            {
                // Primer carácter
                Console.Write(caracter);

                // Huecos
                for (int j = 0; j < i; j++)
                {
                    Console.Write(' ');
                }

                // Último carácter y salto de línea
                Console.WriteLine(caracter);
            }

            // Última fila
            if (filas > 1)
            {
                for (int i = 0; i < filas; i++)
                {
                    Console.Write(caracter);
                }
            }

            Console.WriteLine();
        }

        private static void ProcesarEntrada(out int filas, out char caracter)
        {
            bool filasValidas;
            bool caracterValido;

            do
            {
                Console.WriteLine("Introduzca el número de filas de la pirámide:");

                filasValidas = (int.TryParse(Console.ReadLine(), out filas) && filas > 0);

                if (!filasValidas)
                {
                    Console.WriteLine("Número de filas no válido");
                }

            } while (!filasValidas);

            do
            {
                Console.WriteLine("Introduzca el carácter para rellenar la pirámide:");

                caracterValido = (char.TryParse(Console.ReadLine(), out caracter) && caracter != ' ');

            } while (!filasValidas);

        }
    }
}

```