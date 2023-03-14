# Laboratorio 04 - Linq
## **Se incluyen todos los ejercicios en el mismo código para no tener que copiar todo 6 veces, la idea sería comentarlos todos menos el que se quiera probar**<br/><br/>

## Ejercicio 1 - Extraer la lista de pacientes que sean de la especialidad pediatrics y que tengan menos de 10 años.
<br/>

## Ejercicio 2 - Queremos activar el protocolo de urgencia si hay algún paciente con ritmo cardíaco mayor que 100 o temperatura mayor a 39.
<br/>

## Ejercicio 3 - No podemos atender a todos los pacientes hoy por lo que vamos a crear una nueva coleccion y reasignar a los pacientes de pediatrics a general medicine
<br/>

## Ejercicio 4 - Queremos conocer de una sola consulta el numero de pacientes que estan asignado a general medicine y a pediatrics.
<br/>

## Ejercicio 5 - Devuelve una lista nueva que por cada paciente se indique si tiene prioridad o no. Se tendrá prioridad si el ritmo cardiaco es mayor 100 o la temperatura es mayor a 39.
<br/>

## Ejercicio 6 - Queremos conocer de una sola consulta La edad media de pacientes asignados a pediatrics y general medicine.


``` C#
using System;
using System.Linq;

namespace lab4
{
    public class Patient
    {
        public uint Id { get; set; }
        public string Name { get; set; }
        public string LastName { get; set; }
        public string Sex { get; set; }
        public float Temperature { get; set; }
        public uint HeartRate { get; set; }
        public string Specialty { get; set; }
        public uint Age { get; set; }

        public override string ToString()
        {
            return $"{Id}, {Name}, {LastName}, {Sex}, {Temperature}, {HeartRate}, {Specialty}, {Age}";
        }
    }

    internal class Program
    {
        static void Main(string[] args)
        {
            var patients = new Patient[]
            {
                new Patient
                {
                    Id = 1,
                    Name = "John",
                    LastName = "Doe",
                    Sex = "Male",
                    Temperature = 36.8f,
                    HeartRate = 80,
                    Specialty = "general medicine",
                    Age = 44
                },
                new Patient
                {
                    Id = 2,
                    Name = "Jane",
                    LastName = "Doe",
                    Sex = "Female",
                    Temperature = 36.8f,
                    HeartRate = 70,
                    Specialty = "general medicine",
                    Age = 43
                },new Patient
                {
                    Id = 3,
                    Name = "Junior",
                    LastName = "Doe",
                    Sex = "Male",
                    Temperature = 36.8f,
                    HeartRate = 90,
                    Specialty = "pediatrics",
                    Age = 8
                },new Patient
                {
                    Id = 4,
                    Name = "Mary",
                    LastName = "Wien",
                    Sex = "Female",
                    Temperature = 36.8f,
                    HeartRate = 120,
                    Specialty = "general medicine",
                    Age = 20
                },new Patient
                {
                    Id = 5,
                    Name = "Scarlett",
                    LastName = "Somez",
                    Sex = "Female",
                    Temperature = 36.8f,
                    HeartRate = 110,
                    Specialty = "general medicine",
                    Age = 30
                },new Patient
                {
                    Id = 6,
                    Name = "Brian",
                    LastName = "Kid",
                    Sex = "Male",
                    Temperature = 39.8f,
                    HeartRate = 80,
                    Specialty = "pediatrics",
                    Age = 11
                }
            };

            // Ejercicio 1
            var pediatricsPatientsYoungerThan10 = patients.Where(p => p.Specialty.Equals("pediatrics") && p.Age < 10);
            foreach (var patient in pediatricsPatientsYoungerThan10)
            {
                Console.WriteLine(patient);
            }

            // Ejercicio 2
            // Obtener los pacientes
            var urgencyPatients = patients.Where(p => p.HeartRate > 100 || p.Temperature > 39);
            foreach (var patient in urgencyPatients)
            {
               Console.WriteLine(patient);
            }

            // Activar protocolo si existiera algún paciente
            if (patients.Where(p => p.HeartRate > 100 || p.Temperature > 39).Count() > 0) 
                Console.WriteLine("Activado protocolo de emergencia");

            // Ejercicio 3
            var pediatricsPatients = patients.Where(p => p.Specialty.Equals("pediatrics"));
            foreach (var patient in pediatricsPatients)
            {
                patient.Specialty = "general medicine";
                Console.WriteLine(patient);
            }

            // Ejercicio 4
            var specialties = patients.GroupBy(p => p.Specialty);
            foreach (var specialty in specialties)
               Console.WriteLine($"{specialty.Key} => {specialty.Count()}");

            // Ejercicio 5
            var entries = patients.Select(p => new { Paciente = p, Prioridad = (p.HeartRate > 100 || p.Temperature > 39f)});
            foreach (var entry in entries)
               Console.WriteLine($"{entry.Paciente} => {entry.Prioridad}");

            // Ejercicio 6
            var specialties = patients.GroupBy(p => p.Specialty);
            foreach (var specialty in specialties)
               Console.WriteLine($"{specialty.Key} => {specialty.Average(p => p.Age)}");
        }
    }
}

```