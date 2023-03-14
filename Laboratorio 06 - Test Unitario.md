# Laboratorio 06 - Test Unitario

## Ejercicio 1 - Vamos a crear una aplicación de consola que tiene como funcionalidad solicitar al usuario un correo electrónico y comprobará si es válido. La aplicación seguirá solicitando correos al usuario hasta que se escriba como correo electrónico el comando "quit".

## El objetivo de este ejercicio es crear los tests necesarios para verificar que la función que válida los correos electrónicos funciona correctamente.

## Ejercicio 2 - Modificamos el ejercicio anterior para ir almacenando los correos electrónicos en memoria y en el caso de que el usuario introduzca un correo repetido mostrar el siguiente mensaje:

## "Ya se ha validado el correo electrónico".

## ¿Cómo afecta esta modificación a los tests? Utiliza Moq para aislar la funcionalidad de comprobar si el usuario ya ha introducido el correo.

## ***Código para los dos ejercicios:***

## Program.cs
``` C#
using System.Net.Mail;

public interface IEmailStorage
{
    List<String> GetEmails();
    void AddEmail(string email);
}

public class EmailStorage : IEmailStorage
{
    private readonly List<String> _emails = new List<string>();
    public List<String> GetEmails()
    {
        return _emails;
    }
    public void AddEmail(string email)
    {
        _emails.Add(email);
    }
}

public class EmailValidation
{
    public static bool IsValidEmail(string email)
    {
        bool emailValido;
        try
        {
            MailAddress mail = new MailAddress(email);
            emailValido = true;
        }
        catch (Exception)
        {
            emailValido = false;
        }

        return emailValido;
    }

    public static bool AlreadyValidatedEmail(string email, IEmailStorage storage)
    {
        if (email is null)
        {
            throw new ArgumentNullException("email");
        }
        if (storage is null) 
        { 
            throw new ArgumentNullException("storage");
        }
        return storage.GetEmails().Contains(email);
    }

    public static string ProcesarEntrada(string email, IEmailStorage storage, ref bool terminar)
    {
        if (email.ToLower().Equals("quit"))
        {
            terminar = true;
            return "Ejecución finalizada";
        }
        else if (!AlreadyValidatedEmail(email, storage))
        {
            if (IsValidEmail(email))
            {
                storage.AddEmail(email);
                return $"El email {email} es válido";
            }
            else
            {
                return $"El email {email} no es válido";
            }
        }
        else
        {
            return "Ya se ha validado el correo electrónico";
        }
    }

    public static void Main()
    {
        bool terminar = false;
        var storage = new EmailStorage();

        do
        {
            Console.Write("Introduzca un correo (quit para terminar): ");

            Console.WriteLine(ProcesarEntrada(Console.ReadLine(), storage, ref terminar));
        }
        while (!terminar);

    }
}
```

## IsValidEmailShould.cs
``` C#
namespace Testing.Test
{

    /*
    Casos de prueba

    Positivos:
    1. Correo estándar (example@email.com)
    2. Puntos en la parte local (example.first.middle.lastname@email.com)
    3. Puntos en la parte del dominio (example@subdomain.email.com)
    4. Símbolos + (example+firstname+lastname@email.com)
    5. Dirección IPv4 sin corchetes en la parte del dominio (example@234.234.234.234)
    6. Dirección IPv4 con corchetes en la parte del dominio (example@[234.234.234.234])
    7. Dirección IPv6 con corchetes en la parte del dominio (postmaster@[2001:0db8:85a3:0000:0000:8a2e:0370:7334])
    8. Comillas (“example”@ema”il.com)
    9. Parte local numérica (0987654321@example.com)
    10. Guiones (examp-le@email-one.com)
    11. Parte local formada exclusivamente por barras bajas (_______@email.com)
    12. Espacios antes de correo válido (_______@email.com)
    13. Espacios después de correo válido (example@email.com        )


    Negativos:
    14. Entrada nula (null)
    15. Entrada en blanco ()
    16. Entrada con solo espacios(   )
    17. Espacios en la parte del dominio (example@em ail.com)
    18. Entrada sin parte local (@example.com)
    19. Entrada sin parte del dominio (simple@)
    20. Entrada sin @ (simpleexample.com)
    21. Entrada con demasiadas @ (simple@exam@ple.com)
    22. Entrada con HTML (simple<h1>Hola buenas</h1>@example.com)
    23. Dirección IPv6 sin corchetes en la parte del dominio (postmaster@2001:0db8:85a3:0000:0000:8a2e:0370:7334)

    */

    // Casos positivos

    [TestClass]
    public class IsValidEmailShould
    {
        // 1
        [TestMethod]
        public void Return_True_When_Input_Is_Standard_Email()
        {
            // Arrange
            string email = "example@email.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 2
        [TestMethod]
        public void Return_True_When_Input_Has_Dots_In_Local_Part()
        {
            // Arrange
            string email = "example.first.middle.lastname@email.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 3
        [TestMethod]
        public void Return_True_When_Input_Has_Dots_In_Domain_Part()
        {
            // Arrange
            string email = "example@subdomain.email.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 4
        [TestMethod]
        public void Return_True_When_Input_Has_Plus_Symbols()
        {
            // Arrange
            string email = "example+firstname+lastname@email.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 5
        [TestMethod]
        public void Return_True_When_Input_Has_IPv4_With_No_Brackets_In_Domain_Part()
        {
            // Arrange
            string email = "example@234.234.234.234";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 6
        [TestMethod]
        public void Return_True_When_Input_Has_IPv4_With_Brackets_In_Domain_Part()
        {
            // Arrange
            string email = "example@[234.234.234.234]";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 7
        [TestMethod]
        public void Return_True_When_Input_Has_IPv6_Address_With_Brackets_In_Domain_Part()
        {
            // Arrange
            string email = "postmaster@[2001:0db8:85a3:0000:0000:8a2e:0370:7334]";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 8
        [TestMethod]
        public void Return_True_When_Input_Has_Quotes()
        {
            // Arrange
            string email = "“example”@ema”il.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 9
        [TestMethod]
        public void Return_True_When_Input_Has_Numeric_Local_Part()
        {
            // Arrange
            string email = "0987654321@example.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 10
        [TestMethod]
        public void Return_True_When_Input_Has_Hyphen_Symbols()
        {
            // Arrange
            string email = "examp-le@email-one.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 11
        [TestMethod]
        public void Return_True_When_Input_Has_Only_Underscores_As_Local_Part()
        {
            // Arrange
            string email = "_______@email.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 12
        [TestMethod]
        public void Return_True_When_Input_Has_Preceding_Spaces()
        {
            // Arrange
            string email = "      example@email.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }

        // 13
        [TestMethod]
        public void Return_True_When_Input_Has_Trailing_Spaces()
        {
            // Arrange
            string email = "example@email.com        ";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, true);
        }



        // Casos negativos

        // 14
        [TestMethod]
        public void Return_False_When_Input_Is_Null()
        {
            // Arrange
            string email = null;
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, false);
        }

        // 15
        [TestMethod]
        public void Return_False_When_Input_Is_Blank()
        {
            // Arrange
            string email = "";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, false);
        }

        // 16
        [TestMethod]
        public void Return_False_When_Input_Is_Only_Spaces()
        {
            // Arrange
            string email = "   ";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, false);
        }

        // 17
        [TestMethod]
        public void Return_False_When_Input_Has_Spaces_In_Domain_Part()
        {
            // Arrange
            string email = "example@em ail.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, false);
        }

        // 18
        [TestMethod]
        public void Return_False_When_Input_Has_No_Local_Part()
        {
            // Arrange
            string email = "@example.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, false);
        }

        // 19
        [TestMethod]
        public void Return_False_When_Input_Has_No_Domain_Part()
        {
            // Arrange
            string email = "simple@";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, false);
        }

        // 20
        [TestMethod]
        public void Return_False_When_Input_Has_No_At_Symbol()
        {
            // Arrange
            string email = "simpleexample.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, false);
        }

        // 21
        [TestMethod]
        public void Return_False_When_Input_Has_Too_Many_At_Symbols()
        {
            // Arrange
            string email = "simple@exam@ple.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, false);
        }

        // 22
        [TestMethod]
        public void Return_False_When_Input_Has_Encoded_HTML()
        {
            // Arrange
            string email = "simple<h1>Hola buenas</h1>@example.com";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, false);
        }

        // 23
        [TestMethod]
        public void Return_False_When_Input_Has_IPv6_Address_With_No_Brackets_In_Domain_Part()
        {
            // Arrange
            string email = "postmaster@2001:0db8:85a3:0000:0000:8a2e:0370:7334";
            bool validEmail;

            // Act
            validEmail = EmailValidation.IsValidEmail(email);

            // Assert
            Assert.AreEqual(validEmail, false);
        }
    }
}
```

## AlreadyValidatedEmailShould.cs
``` C#
using Moq;

namespace Testing.Test
{

    /*
   Casos de prueba
   1. Correo ya almacenado -> true
   2. Correo nuevo -> false
   3. Argumento email nulo -> ArgumentNullException
   4. Argumento storage nulo -> ArgumentNullException
   */

    [TestClass]
    public class AlreadyValidatedEmailShould
    {

        // 1
        [TestMethod]
        public void Return_True_When_Email_Is_Stored()
        {
            // Arrange
            string email = "email2@example.com";
            List<string> storedEmails = new List<string>() { "email1@gmail.com", "email2@example.com", "email3@hotmail.com" };

            var emailStorageMock = new Mock<IEmailStorage>();
            emailStorageMock.Setup(esm => esm.GetEmails()).Returns(storedEmails);

            // Act
            bool alreadyValidatedEmail = EmailValidation.AlreadyValidatedEmail(email, emailStorageMock.Object);

            // Assert
            Assert.AreEqual(alreadyValidatedEmail, true);
        }

        // 2
        [TestMethod]
        public void Return_False_When_Email_Is_Stored()
        {
            // Arrange
            string email = "email4@outlook.com";
            List<string> storedEmails = new List<string>() { "email1@gmail.com", "email2@example.com", "email3@hotmail.com" };

            var emailStorageMock = new Mock<IEmailStorage>();
            emailStorageMock.Setup(esm => esm.GetEmails()).Returns(storedEmails);

            // Act
            bool alreadyValidatedEmail = EmailValidation.AlreadyValidatedEmail(email, emailStorageMock.Object);

            // Assert
            Assert.AreEqual(alreadyValidatedEmail, false);
        }

        // 3
        [TestMethod]
        public void Throw_Null_Argument_Exception_When_Email_Is_Null()
        {
            // Arrange
            List<string> storedEmails = new List<string>() { "email1@gmail.com", "email2@example.com", "email3@hotmail.com" };

            var emailStorageMock = new Mock<IEmailStorage>();
            emailStorageMock.Setup(esm => esm.GetEmails()).Returns(storedEmails);

            // Act
            // Assert
            Assert.ThrowsException<ArgumentNullException>(() => EmailValidation.AlreadyValidatedEmail(null, emailStorageMock.Object));
        }

        // 4
        [TestMethod]
        public void Throw_Null_Argument_Exception_When_Storage_Is_Null()
        {
            // Arrange
            string email = "email4@outlook.com";

            // Act
            // Assert
            Assert.ThrowsException<ArgumentNullException>(() => EmailValidation.AlreadyValidatedEmail(email, null));
        }
    }
}
```