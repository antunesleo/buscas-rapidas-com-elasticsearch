# buscas-rapidas-com-elasticsearch

<p align="center">
   <img width="70%" src="assets/cover.png">
</p>
Conteúdo apresentado na palestra **Buscas rápidas com elasticsearch**.

## Parte 01: Index movies
Vamos indexar e buscar ótimos filmes para asssistir hoje!
```

DELETE movie

# Criando o index movies
PUT movies
{
  "mappings": {
    "_doc": {
      "properties": {
        "title" : { "type" : "text" },
        "year": {"type" : "integer"},
        "director": {"type": "text"},
        "stars": {"type": "text"}
      }
    }
  }
}

# Adicionando a properiedade genres ao indice movies existente
PUT movies/_mapping/_doc 
{
  "properties": {
    "genres": {
      "type": "text"
    }
  }
}

# Pegando informações do indice movies (e não dos documentos do indice)
GET movies

# Criando um documento com ID 1 no indice movies, para o filme The GodFather 
POST movies/_doc/1
{
  "title": "The Godfather",
  "year": 1972,
  "director": "Francis Ford Coppola",
  "stars": [
    "Marlon Brando", 
    "Al Pacino", 
    "James Caan"
  ],
  "genres": ["Crime", "Drama"]
}

POST movies/_doc/2
{
  "title": "The Dark Night",
  "year": 2008,
  "director": "Christopher Nolan",
  "stars": [
    "Christian Bale", 
    "Heath Ledger", 
    "Aaron Eckhart"
  ],
  "genres": ["Action", "Crime", "Drama"]
}

POST movies/_doc/3
{
  "title": "The Godfather: Part II",
  "year": 1974,
  "director": "Francis Ford Coppola",
  "stars": [
    "Al Pacino", 
    "Robert De Niro", 
    "Robert Duvall" 
  ],
  "genres": ["Crime", "Drama"]
}

POST movies/_doc/4
{
  "title": "Pulp Fiction",
  "year": 1994,
  "director": "Quentin Tarantino",
  "stars": [
    "John Travolta", 
    "Uma Thurman", 
    "Samuel L. Jackson"
  ],
  "genres": ["Crime", "Drama"]
}

POST movies/_doc/5
{
  "title": "The Lord of the Rings: The Return of the King",
  "year": 2003,
  "director": "Peter Jackson",
  "stars": [
    "Elijah Wood", 
    "Viggo Mortensen", 
    "Ian McKellen", 
    "Orlando Bloom"
  ],
  "genres": ["Action", "Adventure", "Drama"]
}

POST movies/_doc/6
{
  "title": "Schindler's List",
  "year": 1993,
  "director": "Steven Spielberg",
  "stars": [
    "Liam Neeson", 
    "Ralph Fiennes", 
    "Ben Kingsley", 
    "Caroline Goodall"
  ],
  "genres": ["Biography", "Drama", "History"]
}

POST movies/_doc/7
{
  "title": "Se7en",
  "year": 1995,
  "director": "David Fincher",
  "stars": [
    "Morgan Freeman", 
    "Brad Pitt", 
    "Kevin Spacey", 
    "Andrew Kevin Walker"
  ],
  "genres": ["Biography", "Drama", "History"]
}

POST movies/_doc/8
{
  "title": "Inception",
  "year": 2010,
  "director": "Christopher Nolan",
  "stars": [
    "Leonardo DiCaprio", 
    "Joseph Gordon-Levitt", 
    "Ellen Page", 
    "Ken Watanabe"
  ],
  "genres": ["Action", "Adventure", "Sci-Fi"]
}

POST movies/_doc/9
{
  "title": "Interstellar",
  "year": 2014,
  "director": "Christopher Nolan",
  "stars": [
    "Matthew McConaughey", 
    "Anne Hathaway", 
    "Jessica Chastain", 
    "Mackenzie Foy"
  ],
  "genres": ["Adventure", "Drama", "Sci-Fi"]
}

POST movies/_doc/10
{
  "title": "The Wolf of Wall Street",
  "year": 2013,
  "director": "Martin Scorsese",
  "stars": [
    "Leonardo DiCaprio", 
    "Jonah Hill", 
    "Margot Robbie"
  ],
  "genres": ["Biography", "Comedy", "Crime"]
}

POST movies/_doc/11
{
  "title": "The Revenant",
  "year": 2015,
  "director": "Alejandro G. Iñárritu",
  "stars": [
    "Leonardo DiCaprio", 
    "Tom Hardy", 
    "Will Poulter"
  ],
  "genres": ["Action", "Adventure", "Drama"]
}

# Essa é uma das queries mais simples. Traz os filmes do Christopher Nolan
GET movies/_search
{
  "query": {
    "match" : {
      "director" : "Christopher Nolan"
    }
  }
}

# Essa é a bool query, o should e minimal diz que 2 das condições precisam ser verdadeiras. Repare que a query gera score
GET movies/_search
{
  "query": {
    "bool": {
      "should": [
        {"match": {"director" : "Christopher Nolan"}},
        {"match": {"genres": "Sci-Fi"}},
        {
          "range" : {
            "year" : {
              "gte" : 2009
            }
          }
        },
        {"match": {"stars": "Leonardo Dicaprio"}}
      ],
      "minimum_should_match": 2
    }
  }
}

# Essa é a bool query, o should e minimal diz que 2 das condições precisam ser verdadeiras. Repare que a query gera score
GET movies/_search
{
  "query": {
    "bool": {
      "should": [
        {"match": {"director" : "Christopher Nolan"}},
        {"match": {"genres": "Sci-Fi"}},
        {
          "range" : {
            "year" : {
              "gte" : 2009
            }
          }
        },
        {"match": {"stars": "Leonardo Dicaprio"}}
      ],
      "minimum_should_match": 2
    }
  }
}

# Esse é a query filter, é sim ou não, ou traz ou não traz, e não contribui pro score
GET movies/_search
{
  "query": {
    "bool": {
      "filter": [
        {"match": {"genres": "Sci-Fi"}},
        {
          "range" : {
            "year" : {
              "gte" : 2008
            }
          }
        }
      ]
    }
  }
}
```


## Parte 02: Index places
E porque não conhecer um pouco sobre a **Geolocation API**
```
PUT places
{
    "mappings" : {
        "_doc" : {
            "properties" : {
              "name" : { "type" : "text" },
              "address": {"type" : "text"},
              "tags" : { "type" : "text" },
              "location" : {
                "type" : "geo_point"
              }                  
            }
        }
    }
}


# Criando um novo documento no índice places com o ID 1
POST places/_doc/1
{
  "name": "Bar do tião",
  "address": "R. Padre Santo Marino, 10000 - Jardim Candida, Araras - SP, 13603-009", 
  "stars": 5.0,
  "tags": ["bar", "music", "raiz", "beer"],
  "location": {
    "lat": -22.3518421,
    "lon": -47.4068347
  }
}

# Criando um novo documento no índice places com o ID 3
POST places/_doc/2
{
  "name": "Bar hipster",
  "address": "R. Otávio Merlo, 10001 - Jardim Anhanguera, Araras - SP, 13600-240",
  "tags": ["bar", "music", "beer", "hipster"],
  "stars": 4.7,
  "location": {
    "lat": -22.3528461,
    "lon": -47.3988823
  }
}


POST places/_doc/3
{
  "name": "Mercadinho do seu joão",
  "address": "Av. Dona Renata, 10002 - Vila Michelin, Araras - SP, 13601-000",
  "stars": "4.3",
  "tags": ["supermarket",  "food"],
  "location": {
    "lat": -22.3492804,
    "lon": -47.3878011
  }
}


POST places/_doc/4
{
  "name": "Padaria do Agrolão",
  "address": "Av. Dona Renata, 10003 - Centro, Araras - SP, 13600-001",
  "stars": 4.4,
  "tags": ["bakery",  "food", "coffee"],
  "location": {
    "lat": -22.350767,
    "lon": -47.3918487
  }
}

POST places/_doc/5
{
  "name": "Café Cuadinho",
  "address": "R. Alferes José Caetano, 10004 - Centro, Piracicaba - SP, 13400-120",
  "stars": "4.5",
  "tags": ["food", "coffee"],
  "location": {
    "lat": -22.72521,
    "lon": -47.6531076
  }
}

POST places/_doc/6
{
  "name": "Disney Orlando Tickets",
  "address": "12386 FL-535 #111, Orlando, FL 32836, EUA",
  "stars": "3.9",
  "tags": ["food", "coffee"],
  "city": "Orlando",
  "location": {
    "lat":  28.3849065,
    "lon": -81.5095633
  }
}

POST places/_doc/7
{
  "name": "Perebinha Sport Center",
  "address": "Av. Melvin Jones, 10005 - Campinho, Araras - SP, 13607-055",
  "stars": "4.4",
  "tags": ["soccer", "sports"],
  "location": {
    "lat":  -22.3536235,
    "lon": -47.3664665
  }
}

POST places/_doc/7
{
  "name": "Mercadinho",
  "address": "Av. Dona Renata, 10006, Araras - SP, 13600-601",
  "stars": "3.9",
  "tags": ["supermarket", "eletronics", "food"],
  "location": {
    "lat":  -22.3561843,
    "lon": -47.3770557
  }
}


POST places/_doc/8
{
  "name": "Pousada do Viajante",
  "address": "Av. Romana Ometo, 10005 - Jardim Candida, Araras - SP, 13603-004",
  "stars": "4.1",
  "tags": ["hotel", "three stars"],
  "location": {
    "lat":  -22.354293,
    "lon": -47.3978402
  }
}


POST places/_doc/9
{
  "name": "Pousada Cantores da Lua",
  "address": "R. Júlio Mesquita, 10006 - Centro, Araras - SP, 13600-061",
  "stars": "3.9",
  "tags": ["hotel", "three stars"],
  "location": {
    "lat":  -22.355045,
    "lon": -47.393143
  }
}


POST places/_doc/10
{
  "name": "Pousada Morada",
  "address": "R. Bolívia, 10007 - Vila Michelin, Araras - SP, 13601-003",
  "stars": "4.4",
  "tags": ["hotel", "three start"],
  "location": {
    "lat":  -22.361778,
    "lon": -47.380634
  }
}


POST places/_doc/11
{
  "name": "Café Expressinho",
  "address": "Rua 13 de Maio, 10008 - Centro, Araras - SP, 13600-090",
  "stars": "4.6",
  "tags": ["coffee", "food"],
  "city": "Araras",
  "location": {
    "lat":  -22.3533415,
    "lon": -47.3845069
  }
}

POST places/_doc/12
{
  "name": "Café dos sem açucar",
  "address": "R. Tiradentes, 10009 - Centro, Araras - SP, 13600-070",
  "stars": 4.4, 
  "tags": ["coffee", "food"],
  "city": "Araras",
  "location": {
    "lat":  -22.3551565,
    "lon": -47.3836271
  }
}

# Agora vamos fazer alguma queries

# Traz os lugares mais próximo da uniararas em um raio de 10KM
GET places/_search
{
    "query": {
        "bool" : {
            "filter" : {
                "geo_distance" : {
                    "distance" : "10km",
                    "location" : {
                      "lat": -22.3746962, 
                      "lon": -47.3719928
                    }
                }
            }
        }
    },
    "sort" : [
        {
            "_geo_distance" : {
                "location" : {
                  "lat": -22.3746962,
                  "lon": -47.3719928
                },
                "order" : "asc",
                "unit" : "km"
            }
        }
    ]
}

# Traz os supermercados mais próximos da Uniararas em um raio de 10KM
GET places/_search
{
    "query": {
        "bool" : {
            "must" : {
              "match" : { "tags": "supermarket" }
            },
            "filter" : {
                "geo_distance" : {
                    "distance" : "10km",
                    "location" : {
                      "lat": -22.3746962, 
                      "lon": -47.3719928
                    }
                }
            }
        }
    },
    "sort" : [
        {
            "_geo_distance" : {
                "location" : {
                  "lat": -22.3746962,
                  "lon": -47.3719928
                },
                "order" : "asc",
                "unit" : "km"
            }
        }
    ]
}
```
