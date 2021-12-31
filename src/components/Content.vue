<template>
  <h1>Recomendador. Modelos Basados en el contenido</h1>
  <div>
    <label for="input-file">Introduce un archivo:</label><br>
    <input type="file" id="input-file">
  </div>
  <button class="button" v-on:click="getData">Go</button>
  <div class="solucion" id="solucion"></div>
</template>

<script>
export default {
  name: 'Content',
  data() {
    return {
      documents: [],
      statDocuments: [{
        doc_index: Number,
        term_index: Number,
        term: String,
        TF: Number,
        IDF: Number,
        TF_IDF: Number
      }],
      lengths: [],
      simCosArray: [{
        x: Number,
        y: Number,
        sim: Number
      }]
    }
  },
  mounted () {
    this.i = 1
  },
  methods: {
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
    readFileContent(file) {
      const reader = new FileReader()
      return new Promise((resolve, reject) => {
        reader.onload = event => resolve(event.target.result)
        reader.onerror = error => reject(error)
        reader.readAsText(file, "UTF-8")
      })
    },
    Resultado() {
      document.getElementById("solucion").innerHTML = '';              
      // Creamos la solucion
      const page = document.getElementById("solucion");
      const list = document.createElement("ul")                        
      // Creamos una pagina
      let i = 0                                                        
      // Añadimos un indice extra
      this.documents.forEach((element, index) => {
        const doc = document.createElement("li")
        doc.innerHTML = element
        list.appendChild(doc)                                          
        // Añadimos cada documento a la lista
        const sublist = document.createElement("ul")                  
         // Creamos una sublista por cada documento
        while (this.statDocuments[i].doc_index === index) {            
          // Comprobamos si el indice de de los terminos coinciden con el de los documentos
          const term = document.createElement("li")                    
          // Si coincide lo añadimos a la sublista
          term.innerHTML = this.statDocuments[i].term_index 
                           + " - " + this.statDocuments[i].term
                           + " - " + "TF: " +this.statDocuments[i].TF
                           + " - " + "IDF: " + this.statDocuments[i].IDF
                           + " - " + "TF-IDF: " + this.statDocuments[i].TF_IDF
          sublist.appendChild(term)
          i = i + 1                                                    
          // Avanzamos en el vector
          if (i === this.statDocuments.length) break                   
          // Hasta que no supere la longitud del vector statsDocuments
        }
        list.appendChild(sublist)
      })
      page.appendChild(list)
      const simCosList = document.createElement("ul");
      this.simCosArray.forEach((element, index) => {
        if (index !== 0) {
          const simTerm = document.createElement("li");
          simTerm.innerHTML = "simCos(" + element.x + ", " + element.y + ") = " + element.sim;
          simCosList.appendChild(simTerm)
        }
      })
      page.appendChild(simCosList)
      

    },
    calculateTF_IDF() { 
      for (let i = 0; i < this.statDocuments.length; i++) {
        let sameDocCount = 0
        let otherDocCount = new Set()
        otherDocCount.add("Initialized")
        for (let j = 0; j < this.statDocuments.length; j++) {
          if (this.statDocuments[i].term === this.statDocuments[j].term) {
            if (this.statDocuments[i].doc_index === this.statDocuments[j].doc_index) {
              sameDocCount = sameDocCount + 1
            } else {
              otherDocCount.add(this.statDocuments[j].doc_index)
            }
          }
        }
        this.statDocuments[i].TF = (sameDocCount / this.lengths[this.statDocuments[i].doc_index]).toFixed(3);
        this.statDocuments[i].IDF = (Math.log(this.documents.length / otherDocCount.size) / Math.log(10)).toFixed(3)
        this.statDocuments[i].TF_IDF = (this.statDocuments[i].TF * this.statDocuments[i].IDF).toFixed(4)
      }
    },
    simCos() {
      for (let i = 0; i < this.documents.length; i++) {
        for (let j = 0; j < this.documents.length; j++) {
          if (i === j) break
          let sum = 0;
          this.statDocuments.forEach(element => {
            this.statDocuments.forEach(element2 => {
              if (i === element.doc_index && j === element2.doc_index && element.term === element2.term) {
                sum += element.TF_IDF * element2.TF_IDF
              }
            })
          })
          this.simCosArray.push({x: i, y: j, sim: (sum).toFixed(5)})
        }
      }
    }
  }
}
</script>

<style>


input {
  margin: 10px;
}

.button {
  margin: 50px;
  height: 50px;
  width: 60px;
  background-color: rgb(18, 211, 82);
  border-radius: 8px;
}

ul li {
  text-align: left;
  margin-top: 20px;
  font-weight: bold;
}

ul {
  margin-left: 30px;
  margin-right: 30px;
}
</style>