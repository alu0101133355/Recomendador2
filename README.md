# Sistemas de recomendacion. Metodos de filtrado Colaborativo

## Para ejecutar:

  ```
  npm install
  npm run server
  ```

 ## Codigo realizado
 
Para el desarrollo de la aplicacion he utilizado Vue que se basa en componentes. Entre los compoenentes realizado se ha creado App.vue, fichero que es montado por nuestro main.js y que importa los componentes Header que es nuestra cabecera y tambien carga el Content.vue.
 
Nuestra aplñicacion visualmente se ve de la siguiente forma:
 
 IMAGEN CABECERA
 
Cuando apretamos el boton go, se le llama a la funcion getData(), que recopila los datos del fichero que se ha cargado:

```
getData() {
      alert("El programa puede tardar un tiempo")
      this.documents = []
      this.statDocuments = []
      const file = document.getElementById("input-file").files[0];  
      // Leemos el contenido del fichero
      this.readFileContent(file).then(result => {
        const stringArray = result.split("\n")                      
        // Dividimos todo el texto en documentos (oraciones)
        stringArray.forEach((element, doc_index) => {               
          // Cada oracion es un element, junto con su indice
          this.documents.push(element)                              
          // Añadimos el documento al vector de documentos
          element = element.replace(/,/g,'')                       
           // Quitamos las comas
          element = element.replaceAll('.', '');                    
          // Quitamos los puntos
          element = element.toLowerCase()                           
          // Modificamos todas las palabras a minuscula
          const stringDocuments = element.split(" ")                
          // Dividmos cada oraciones en palabras (terminos)
          this.lengths.push(stringDocuments.length)
          stringDocuments.forEach((term, term_index) => {                       
            // Añadimos el indice del documento, el indice del termino, y el propio termino
            this.statDocuments.push({
              doc_index,
              term_index,
              term
            })
          })
        })
        this.calculateTF_IDF()
        this.simCos()
        this.Resultado()
      });
    },
    
 ```
 
