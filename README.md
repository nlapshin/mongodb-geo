Николай Лапшин. Senior Full Stack.

NodeJS. MongoDb.

Road.Travel. Мы занимаемся поездками на машине.

Долгота - longitude [-180, 180]
Широта - latitude [-90, 90]

[ -110, 70 ]

<!-- 55.68834511689159, 37.5396479740002 -->

// Тип Point
{
  "type": "Point", 
  "coordinates": [ -110, 70 ]
}

// Тип линия
{
  "type": "LineString", 
  "coordinates": [[ -110, 70 ], [ -110, 75 ]]
}

// Тип полигон
{
  "type": "Polygon", 
  "coordinates": [
    [ -110, 70 ], 
    [ -110, 75 ], 
    [ -120, 75 ],
  ]
}

{
    "_id" : ObjectId("55cba2476c522cafdb053ade"),
    "location" : {
        "coordinates" : [ 
            -73.961704, 
            40.662942
        ], // Долгота и широта координаты
        "type" : "Point" // Тип geo объекта
    },
    "name" : "Wendy'S"
}


https://github.com/nlapshin/mongodb-geo
https://github.com/BorisPlus/mongodb_geo

// 1 Задача. Найти ближайшие: банкоматы, рестораны, больницы, и т.д. 
// Я нахожусь в точке.

// 1. Есть входные координаты.
// 2. Ближайшие 10 километров.
// 3. Результат был отсортирован по дистанции от ближайшего.

// Проблема - хранить данные удобно в координатах
// Высчитывать дистанцию.


// Где я?


## Создаем индексы

`
db.restaurants.createIndex({ location: "2dsphere" })
db.neighborhoods.createIndex({ geometry: "2dsphere" })
`

### near

https://www.mongodb.com/docs/manual/reference/operator/query/near/

<!-- near - это близкий. -->
// Система вычисления метры
// minDistance - ми

`
db.restaurants.find({
  location: {
     $near: {
       $geometry: {
          type: "Point",
          coordinates: [ -74.00589459465301, 40.73613449112613 ]
       },
       $minDistance: 1000,
       $maxDistance: 5000
     }
   }
})
`

const place = {
  placeId: '12345',
  localtion: {
    type: "Poligon",
    coordinates: [ -74.00589459465301, 40.73613449112613 ]
  },
}

### geoIntersects

// intersect - пересечение.

https://www.mongodb.com/docs/manual/reference/operator/query/geoIntersects/#mongodb-query-op.-geoIntersects

`
db.neighborhoods.find({ 
  geometry: { 
    $geoIntersects: { 
      $geometry: { 
        type: "Point", 
        coordinates: [ -73.93414658, 40.82302903 ] 
      } 
    } 
  } 
})

db.getCollection('neighborhoods').find({ 
  geometry: { 
    $geoIntersects: { 
      $geometry: { 
        type: "Point", 
        coordinates: [ -73.9576542374368, 40.81622590579082] 
      } 
    } 
  } 
})
`

### geoWithin

https://www.mongodb.com/docs/manual/reference/operator/query/geoWithin/#mongodb-query-op.-geoWithin

`
const neighborhood = db.neighborhoods.findOne({ 
  geometry: { 
    $geoIntersects: { 
      $geometry: { 
        type: "Point", 
        coordinates: [ -73.93414657, 40.82302903 ] 
      } 
    } 
  } 
})

db.restaurants.find( { 
  location: { 
    $geoWithin: { 
      $geometry: neighborhood.geometry 
    } 
  } 
}).count()

`

https://www.mongodb.com/docs/manual/tutorial/calculate-distances-using-spherical-geometry-with-2d-geospatial-indexes/

`
db.restaurants.find({

  location: { 
    $geoWithin: { 
      $centerSphere: [ 
        [ -73.93414657, 40.82302903 ],  // координаты
        10 / 6378.1 // 10 / delimer
      ] 
    } 
  }
})
`
