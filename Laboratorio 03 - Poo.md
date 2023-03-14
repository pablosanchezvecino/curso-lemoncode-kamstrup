# Laboratorio 03 - Poo

## Ejercicio 1 - Read Books

## Crea una función isBookRead que reciba una lista de libros y un título y devuelva si se ha leído o no dicho libro. Un libro es un objeto con title como string y isRead como booleano. En caso de no existir el libro devolver false.

``` C#
using System;
using System.Linq;

namespace lab3
{
    public class Book
    {
        public string Title { get; set; }
        public bool IsRead { get; set; }
    }

    internal class Program
    {
        static void Main(string[] args)
        {
            var books = new Book[] {
               new Book
               {
                   Title = "Harry Potter y la piedra filosofal",
                   IsRead = true
               },
               new Book
               {
                   Title = "Canción de hielo y fuego",
                   IsRead = false
               },
               new Book
               {
                   Title = "Devastación",
                   IsRead = true
               },
            };

            Console.WriteLine(IsBookRead(books, "Devastación")); // true
            Console.WriteLine(IsBookRead(books, "Canción de hielo y fuego")); // false
            Console.WriteLine(IsBookRead(books, "Los Pilares de la Tierra")); // false
        }

        public static bool IsBookRead(Book[] books, string titleToSearch)
        {
            // Utilizando bucle foreach

            //foreach (var book in books)
            //{
            //    if (book.Title.Equals(titleToSearch) && book.IsRead)
            //    {
            //        return true;
            //    }
            //}
            //return false;

            // Utilizando Linq 
            return books.Where(b => b.Title.Equals(titleToSearch) && b.IsRead).FirstOrDefault() is not null;
        }
    }
}

```


## Ejercicio 2 - Slot Machine

## El objetivo de este ejercicio es crear una máquina tragaperras utilizando clases donde cada vez que juguemos insertemos una moneda. Cada máquina tragaperras (instancia) tendrá un contador de monedas que automáticamente se irá incrementando conforme vayamos jugando.

## Cuando se llame al método play el número de monedas se debe incrementar de forma automática y debe generar tres booleanos aleatorios que representarán el estado de las 3 ruletas. El usuario habrá ganado en caso de que los tres booleanos sean true, y por tanto deberá mostrarse por consola el mensaje:

## "Congratulations!!!. You won <número de monedas> coins!!";
## y reiniciar las monedas almacenadas, ya que las hemos conseguido y han salido de la máquina. En caso contrario deberá mostrar otro mensaje:

## "Good luck next time!!".
## Se preguntará al usuario si quiere jugar o dejar.

## Si el usuario elige jugar. Se llamará al método play.
## Si usuario elige cualquier otra opción se terminará la ejecución de la aplicación.
``` C#
using System;

namespace lab3
{

    public class SlotMachine
    {
        private uint _monedas = 0;

        public void play()
        {
            _monedas++;

            bool[] ruletas = new bool[3];
            Random rnd = new Random();

            for (int i = 0; i < ruletas.Length; i++)
            {
                ruletas[i] = (rnd.NextDouble() >= 0.5);
            }
            Console.WriteLine($"{ruletas[0]}, {ruletas[1]}, {ruletas[2]}");

            if (ruletas[0] && ruletas[1] && ruletas[2])
            {
                Console.WriteLine($"Congratulations!!!. You won {_monedas} coins!!");
                _monedas = 0;
            }
            else
            {
                Console.WriteLine("Good luck next time!!");
            }
        }
    }

    internal class Program
    {
        static void Main(string[] args)
        {
            SlotMachine sm = new SlotMachine();

            bool terminar = false;

            while (!terminar)
            {
                sm.play();
                Console.WriteLine("Introduzca \"jugar\" para volver a jugar o cualquier otra entrada para terminar");
                terminar = !Console.ReadLine().ToLower().Equals("jugar");
            }
        }
    }
}

```