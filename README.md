## Создаем индексы

`
db.restaurants.createIndex({ location: "2dsphere" })
db.neighborhoods.createIndex({ geometry: "2dsphere" })
`

### near

https://www.mongodb.com/docs/manual/reference/operator/query/near/

`
db.restaurants.find({
    location: {
     $near: {
       $geometry: {
          type: "Point" ,
          coordinates: [ -74.00589459465301, 40.73613449112613 ]
       },
       $maxDistance: 5000
     }
   }
})
`

### geoIntersects

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

db.restaurants.find( { location: { $geoWithin: { $geometry: neighborhood.geometry } } } ).count()

`

https://www.mongodb.com/docs/manual/tutorial/calculate-distances-using-spherical-geometry-with-2d-geospatial-indexes/

`
db.restaurants.find({ 
  location: { 
    $geoWithin: { 
      $centerSphere: [ 
        [ -73.93414657, 40.82302903 ], 
        10 / 6378.1
      ] 
    } 
  }
})
`
