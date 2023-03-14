# Laboratorio 02 - Excepciones

## Ejercicio 1 – Controla las excepciones posibles que se pueden producir en la aplicación.
``` C#
using System;

namespace lab2
{
    internal class Program
    {
        static void Main(string[] args)
        {
            try
            {
                int intentosRestantes = 10;
                bool acierto = false;
                int numero = -1;
                bool numeroValido = false;

                int objetivo = new Random().Next(100);
                // Console.WriteLine($"Objetivo: {objetivo}");

                while (!acierto && intentosRestantes > 0)
                {
                    do
                    {
                        Console.WriteLine($"Intentos restantes: {intentosRestantes}");
                        Console.WriteLine("Introduce un número:");
                        try
                        {
                            numero = int.Parse(Console.ReadLine());
                            numeroValido = true;
                        }
                        catch (FormatException) // Se introduce un string no parseable
                        {
                            Console.WriteLine("Entrada no válida. Por favor, introduzca un entero\n");
                            numeroValido = false;
                        }
                        catch (OverflowException) // Se introduce un entero demasiado largo, ya sea positivo o negativo
                        {
                            Console.WriteLine("Entrada no válida. El entero introducido no puede ser representado con una variable para enteros de 32 bits\n");
                            numeroValido = false;
                        }

                    } while (!numeroValido);

                    if (numero < objetivo)
                    {
                        Console.WriteLine("El número es más grande\n");
                        intentosRestantes--;
                    }
                    else if (numero > objetivo)
                    {
                        Console.WriteLine("El número es más pequeño\n");
                        intentosRestantes--;
                    }
                    else
                    {
                        Console.WriteLine("Has acertado el número");
                        acierto = true;
                    }
                }

                if (intentosRestantes == 0)
                {
                    Console.WriteLine("Se han agotado los intentos permitidos");
                }

            }
            catch (Exception ex) // Para cualquier otra excepción que se nos haya podido pasar
            {
                Console.WriteLine(ex.Message);
            }
        }

    }
}


```

## Ejercicio 2 – Crea una excepción personalizada para lanzarla cuando el número es mayor o menor.
``` C#
using System;

namespace lab2
{

    public class NumeroDistintoAObjetivoException : Exception
    {
        public NumeroDistintoAObjetivoException(string message) : base(message) { }
    }



    internal class Program
    {
        static void Main(string[] args)
        {
            try
            {
                int intentosRestantes = 10;
                bool acierto = false;
                int numero = 0;
                bool numeroValido = false;

                int objetivo = new Random().Next(100);
                // Console.WriteLine($"Objetivo: {objetivo}");

                while (!acierto && intentosRestantes > 0)
                {
                    do
                    {
                        Console.WriteLine($"Intentos restantes: {intentosRestantes}");
                        Console.WriteLine("Introduce un número:");
                        try
                        {
                            numero = int.Parse(Console.ReadLine());
                            numeroValido = true;
                        }
                        catch (FormatException) // Se introduce un string no parseable
                        {
                            Console.WriteLine("Entrada no válida. Por favor, introduzca un entero\n");
                            numeroValido = false;
                        }
                        catch (OverflowException) // Se introduce un entero demasiado largo, ya sea positivo o negativo
                        {
                            Console.WriteLine("Entrada no válida. El entero introducido no puede ser representado con una variable para enteros de 32 bits\n");
                            numeroValido = false;
                        }

                    } while (!numeroValido);

                    try
                    {
                        if (numero < objetivo)
                        {
                            throw new NumeroDistintoAObjetivoException("El número es más grande\n");
                        }
                        else if (numero > objetivo)
                        {
                            throw new NumeroDistintoAObjetivoException("El número es más pequeño\n");
                        }
                        else
                        {
                            Console.WriteLine("Has acertado el número");
                            acierto = true;
                        }
                    }
                    catch (NumeroDistintoAObjetivoException ex)
                    {
                        Console.WriteLine(ex.Message);
                        intentosRestantes--;
                    }
                }

                if (intentosRestantes == 0)
                {
                    Console.WriteLine("Se han agotado los intentos permitidos");
                }

            }
            catch (Exception ex) // Para cualquier otra excepción que se nos haya podido pasar
            {
                Console.WriteLine(ex.Message);
            }
        }

    }
}

```