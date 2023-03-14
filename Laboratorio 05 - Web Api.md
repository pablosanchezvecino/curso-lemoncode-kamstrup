# Laboratorio 05 - Web Api

## Ejercicio 1 - Implementar los métodos que han quedado pendientes en la teoria:

## - Obtener un Actor por Id.
## - Modificar un Actor.
## - Borrar un Actor.
<br/>

## Código clase Pelicula (solo se ha generado el método Equals)

``` C#
namespace DemoRestApi.Models
{
    public class Pelicula
    {
        public int Id { get; set; }
        public string Titulo { get; set; }
        public int FechaPublicacion { get; set; }

        public override bool Equals(object obj)
        {
            return obj is Pelicula pelicula &&
                   Id == pelicula.Id &&
                   Titulo == pelicula.Titulo &&
                   FechaPublicacion == pelicula.FechaPublicacion;
        }
    }
}
```

## Código clase ActorController

``` C#
using DemoRestApi.Contracts;
using DemoRestApi.Models;
using Microsoft.AspNetCore.Mvc;
using System;
using System.Collections.Generic;

namespace DemoRestApi.Controllers
{
    [Route("api/actores")]
    [ApiController]
    public class ActorController : ControllerBase
    {
        private readonly IActorRepository _actorRepository;

        public ActorController(IActorRepository actorRepository)
        {
            _actorRepository = actorRepository;
        }

        [HttpPost]
        public ActionResult CreateActor(Actor actor)
        {
            try
            {
                _actorRepository.AddActor(actor);
                return Ok();
            }
            catch (Exception ex)
            {
                return BadRequest(ex.Message);
            }
        }



        [HttpGet]
        public ActionResult<List<Actor>> Get()
        {
            return _actorRepository.GetActors();
        }

        [HttpGet("{id}")]
        public ActionResult<Actor> GetById(int id)
        {
            try
            {
                return _actorRepository.GetActorById(id);
            }
            catch (Exception ex)
            {
                return BadRequest(ex.Message);
            }
        }

        [HttpDelete("{id}")]
        public ActionResult Delete(int id)
        {
            try
            {
                _actorRepository.DeleteActor(id);
                return Ok();
            }
            catch (Exception ex)
            {
                return BadRequest(ex.Message);
            }
        }

        [HttpPut]
        public ActionResult Update(Actor actor)
        {
            try
            {
                _actorRepository.UpdateActor(actor);
                return Ok();
            }
            catch (Exception ex)
            {
                return BadRequest(ex.Message);
            }
        }
    }
}
```

## Código clase ActorRepository

``` C#
using DemoRestApi.Contracts;
using DemoRestApi.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

namespace DemoRestApi.Repositories
{
    public class ActorRepository : IActorRepository
    {
        const string JSON_PATH = @"C:\workspace\DemoRestApi\DemoRestApi\Resources\Actores.json";

        private string GetActorsFromFile()
        {
            var json = File.ReadAllText(JSON_PATH);
            return json;
        }

        private void UpdateActores(List<Actor> actores)
        {
            var actoresJson = JsonConvert.SerializeObject(actores, Formatting.Indented);
            File.WriteAllText(JSON_PATH, actoresJson);
        }

        public void AddActor(Actor actor)
        {
            var actores = GetActors();
            var existeActor = actores.Exists(a => a.Id == actor.Id);
            if (existeActor)
            {
                throw new Exception("Ya existe un actor con ese id");
            }
            actores.Add(actor);
            UpdateActores(actores);

        }

        public void DeleteActor(int id)
        {
            var actores = GetActors();
            var actor = actores.Where(a => a.Id == id).FirstOrDefault();
            if (actor is null)
            {
                throw new Exception("No existe ningún artículo con ese id");
            }
            actores.Remove(actor);
            UpdateActores(actores);
        }

        public Actor GetActorById(int id)
        {
            try
            {
                var actor = GetActors().Where(a => a.Id == id).FirstOrDefault();
                if (actor is null)
                {
                    throw new Exception("No existe actor con ese id");
                }
                return actor;
            }
            catch (Exception)
            {
                throw;
            }
        }

        public List<Actor> GetActors()
        {
            try
            {
                var actoresFromFile = GetActorsFromFile();
                List<Actor> actores = JsonConvert.DeserializeObject<List<Actor>>(actoresFromFile);
                return actores;
            }
            catch (Exception)
            {
                throw;
            }
        }

        public void UpdateActor(Actor actor)
        {
            try
            {
                var actores = GetActors();
                var targetActor = actores.Where(a => a.Id == actor.Id).FirstOrDefault();
                if (targetActor is null)
                {
                    throw new Exception("No existe actor con ese id");
                }

                if (!targetActor.Nombre.Equals(actor.Nombre))
                {
                    targetActor.Nombre = actor.Nombre;
                }
                if (!targetActor.Apellido.Equals(actor.Apellido))
                {
                    targetActor.Apellido = actor.Apellido;
                }
                // Compara los valores de las propiedades en lugar de las referencias utilizando el método Equals que hemos generado en la clase Pelicula
                if (!targetActor.Peliculas.SequenceEqual(actor.Peliculas))
                {
                    targetActor.Peliculas = actor.Peliculas;
                }

                UpdateActores(actores);
            }
            catch (Exception)
            {
                throw;
            }
        }
    }
}
```


## Ejercicio 2 - Crear una nueva Web API para la gestión de un almacen. El controlador deberá permitir las siguientes funciones:

## Añadir nuevo Artículo. Un artículo tendrá los siguientes campos:

## - Id.
## - Nombre.
## - Descripción.
## - Fecha de entrada.
## - Cantidad.

## Entrada de artículo. Donde se indica la cantidad a incrementar y el identificador del articulo. Por ejemplo, la llamada a entrada del artículo con id igual a 1 y 200 en cantidad establecerá el articulo con Id 1 con la cantidad que tuviera mas 200 unidades. Control de errores:

## - En el caso de no existir el articulo se deberá devolver un NotFound.

## - Retirada de artículo. Donde se indica la cantidad a decrementar y el identificador del articulo. Por ejemplo, la llamada a retirada del artículo con id igual a 1 y 200 en cantidad establecerá el articulo con Id 1 con la cantidad que tuviera menos 200 unidades. Control de errores:

## - En el caso de no existir el articulo se deberá devolver un NotFound.
## Si no existe suficiente genero en el almacen, se deberá devolver un BadRequest con el mensaje de la excepción.
<br/>

## JSON artículos

``` JSON
[
  {
    "Id": 1,
    "Nombre": "Camiseta Deportiva",
    "Descripcion": "Estas camisetas son especiales para actividades físicas, suelen ser de materiales sintéticos delgados que transpiran para mantener a las personas frescas, permitiendo que su rendimiento físico aumente",
    "FechaEntrada": 1000,
    "Cantidad": 195
  },
  {
    "Id": 2,
    "Nombre": "Pantalones",
    "Descripcion": "Pantalones de color verde",
    "FechaEntrada": 2000,
    "Cantidad": 100
  },
  {
    "Id": 3,
    "Nombre": "Camisa",
    "Descripcion": "Camisa elegante de cuadros",
    "FechaEntrada": 3000,
    "Cantidad": 87
  },
  {
    "Id": 5,
    "Nombre": "Chaqueta",
    "Descripcion": "Chaqueta polar de invierno",
    "FechaEntrada": 5000,
    "Cantidad": 346
  },
  {
    "Id": 6,
    "Nombre": "Reloj",
    "Descripcion": "Reloj de pulsera digital",
    "FechaEntrada": 6000,
    "Cantidad": 535
  }
]
```

## Código clase Startup

``` C#
using AlmacenRestApi.Contracts;
using AlmacenRestApi.Repositories;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.OpenApi.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace AlmacenRestApi
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {

            services.AddControllers();
            services.AddSwaggerGen(c =>
            {
                c.SwaggerDoc("v1", new OpenApiInfo { Title = "AlmacenRestApi", Version = "v1" });
            });
            services.AddTransient<IArticuloRepository, ArticuloRepository>();

        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
                app.UseSwagger();
                app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "AlmacenRestApi v1"));
            }

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllers();
            });
        }
    }
}
```

## Código clase Articulo

``` C#
namespace AlmacenRestApi.Models
{
    public class Articulo
    {
        public int Id { get; set; }
        public string Nombre { get; set; }
        public string Descripcion { get; set; }
        public long FechaEntrada { get; set; }
        public uint Cantidad { get; set; }
    }
}
```
## Código interfaz IArticuloRepository

``` C#
using AlmacenRestApi.Models;
using System.Collections.Generic;

namespace AlmacenRestApi.Contracts
{
    public interface IArticuloRepository
    {
        List<Articulo> GetArticulos();
        Articulo GetArticuloById(int id);
        void AddArticulo(Articulo articulo);
        void UpdateArticulo(Articulo articulo);
        void DeleteArticulo(int id);
        void EntradaArticulo(int id, uint incremento);
        void RetiradaArticulo(int id, uint decremento);
    }
}
```

## Código clase ArticuloRepository

``` C#
using AlmacenRestApi.Contracts;
using AlmacenRestApi.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Numerics;

namespace AlmacenRestApi.Repositories
{
    public class InvalidIdException : Exception
    {
        public InvalidIdException(string message) : base(message) { }
    }

    public class InvalidAmountException : Exception
    {
        public InvalidAmountException(string message) : base(message) { }
    }

    public class ArticuloRepository : IArticuloRepository
    {
        const string JSON_PATH = @"C:\workspace\AlmacenRestApi\AlmacenRestApi\Resources\Articulos.json";
        public void AddArticulo(Articulo articulo)
        {
            var articulos = GetArticulos();
            if (articulos.Exists(a => a.Id == articulo.Id))
            {
                throw new InvalidIdException("Ya existe un artículo con ese identificador");
            }
            articulos.Add(articulo);
            UpdateArticulos(articulos);
        }

        public void DeleteArticulo(int id)
        {
            var articulos = GetArticulos();
            var articulo = articulos.Where(a => a.Id == id).FirstOrDefault();
            if (articulo is null)
            {
                throw new InvalidIdException("No existe ningún artículo con ese id");
            }
            articulos.Remove(articulo);
            UpdateArticulos(articulos);
        }

        public void EntradaArticulo(int id, uint incremento)
        {
            var articulos = GetArticulos();
            var articulo = articulos.Where(a => a.Id == id).FirstOrDefault();
            if (incremento <= 0)
            {
                throw new InvalidAmountException("El número de unidades de entrada debe ser mayor o igual a 1");
            }
            if (articulo is null)
            {
                throw new InvalidIdException("No existe ningún artículo con ese id");
            }
            articulo.Cantidad += incremento;
            UpdateArticulos(articulos);
        }

        public Articulo GetArticuloById(int id)
        {
            try
            {
                var articulo = GetArticulos().Where(a => a.Id == id).FirstOrDefault();
                if (articulo is null)
                {
                    throw new InvalidIdException("No existe ningún artículo con ese id");
                }
                return articulo;
            }
            catch (Exception)
            {
                throw;
            }
        }

        public List<Articulo> GetArticulos()
        {
            try
            {
                var articulosFromFile = GetArticulosFromFile();
                List<Articulo> listArticulos = JsonConvert.DeserializeObject<List<Articulo>>(articulosFromFile);
                return listArticulos;
            }
            catch
            {
                throw;
            }
        }

        public void RetiradaArticulo(int id, uint decremento)
        {
            var articulos = GetArticulos();
            var articulo = articulos.Where(a => a.Id == id).FirstOrDefault();
            if (articulo is null)
            {
                throw new InvalidIdException("No existe ningún artículo con ese id");
            }
            if (articulo.Cantidad < decremento)
            {
                throw new InvalidAmountException("La cantidad de unidades a retirar no puede ser mayor que la del stock disponible del artículo");
            }
            if (decremento <= 0)
            {
                throw new InvalidAmountException("El número de unidades a retirar debe ser mayor o igual a 1");
            }

            articulo.Cantidad -= decremento;
            UpdateArticulos(articulos);
        }

        public void UpdateArticulo(Articulo articulo)
        {
            try
            {
                var articulos = GetArticulos();
                var targetArticulo = articulos.Where(a => a.Id == articulo.Id).FirstOrDefault();
                if (targetArticulo is null)
                {
                    throw new Exception("No existe actor con ese id");
                }

                if (!targetArticulo.Nombre.Equals(articulo.Nombre))
                {
                    targetArticulo.Nombre = articulo.Nombre;
                }
                if (!targetArticulo.Descripcion.Equals(articulo.Descripcion))
                {
                    targetArticulo.Descripcion = articulo.Descripcion;
                }
                if (!targetArticulo.FechaEntrada.Equals(articulo.FechaEntrada))
                {
                    targetArticulo.FechaEntrada = articulo.FechaEntrada;
                }
                if (!targetArticulo.Cantidad.Equals(articulo.Cantidad))
                {
                    targetArticulo.Cantidad = articulo.Cantidad;
                }

                UpdateArticulos(articulos);
            }
            catch (Exception)
            {
                throw;
            }
        }

        private string GetArticulosFromFile()
        {
            return File.ReadAllText(JSON_PATH);
        }

        private void UpdateArticulos(List<Articulo> articulos)
        {
            var articulosJson = JsonConvert.SerializeObject(articulos, Formatting.Indented);
            File.WriteAllText(JSON_PATH, articulosJson);
        }
    }
}
```

## Código clase ArticuloController

``` C#
using AlmacenRestApi.Contracts;
using AlmacenRestApi.Models;
using AlmacenRestApi.Repositories;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using System;
using System.Collections.Generic;
using System.Numerics;

namespace AlmacenRestApi.Controllers
{
    [Route("api/articulos")]
    [ApiController]
    public class ArticuloController : ControllerBase
    {
        private readonly IArticuloRepository _articuloRepository;
        public ArticuloController(IArticuloRepository articuloRepository)
        {
            _articuloRepository = articuloRepository;
        }

        [HttpGet]
        public ActionResult<List<Articulo>> GetAll()
        {
            return _articuloRepository.GetArticulos();
        }

        [HttpGet("{id}")]
        public ActionResult<Articulo> GetById(int id)
        {
            try
            {
                return _articuloRepository.GetArticuloById(id);
            }
            catch (Exception ex)
            {
                return BadRequest(ex.Message);
            }
        }

        [HttpPost]
        public ActionResult CreateArticulo(Articulo articulo)
        {
            try
            {
                _articuloRepository.AddArticulo(articulo);
                return Ok();
            }
            catch (Exception ex)
            {
                return BadRequest(ex.Message);
            }
        }

        [HttpDelete("{id}")]
        public ActionResult Delete(int id)
        {
            try
            {
                _articuloRepository.DeleteArticulo(id);
                return Ok();
            }
            catch (Exception ex)
            {
                return BadRequest(ex.Message);
            }
        }

        [HttpPost("{id}/entrada")]
        public ActionResult Entrada(int id, uint incremento)
        {
            try
            {
                _articuloRepository.EntradaArticulo(id, incremento);
                return Ok();
            }
            catch (InvalidIdException ex)
            {
                return NotFound(ex.Message);
            }
            catch (InvalidAmountException ex)
            {
                return BadRequest(ex.Message);
            }
        }
        [HttpPost("{id}/retirada")]
        public ActionResult Retirada(int id, uint decremento)
        {
            try
            {
                _articuloRepository.RetiradaArticulo(id, decremento);
                return Ok();
            }
            catch (InvalidIdException ex)
            {
                return NotFound(ex.Message);
            }
            catch (InvalidAmountException ex)
            {
                return BadRequest(ex.Message);
            }
        }

        [HttpPut]
        public ActionResult Update(Articulo articulo)
        {
            try
            {
                _articuloRepository.UpdateArticulo(articulo);
                return Ok();
            }
            catch (Exception ex)
            {
                return BadRequest(ex.Message);
            }
        }
    }
}
```