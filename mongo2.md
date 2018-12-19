1.
```
db.airlines.aggregate(
  [
    {
      $group: {
        _id: '$class',
        total: {
          $sum: 1
        }
      }
    }, 
    {
      $project: {
        _id: 0,
        class: '$_id',
        total: 1
      }
    }
  ]
)
```

```sh
{ "total" : 140343, "class" : "F" }
{ "total" : 23123, "class" : "L" }
{ "total" : 5683, "class" : "P" }
{ "total" : 17499, "class" : "G" }
```

2.
```
db.airlines.aggregate(
  [
    {
      $match: {
        destCountry: {
          $ne: 'United States'
        }
      }
    }, 
    {
      $group: {
        _id: '$destCity',
        avgPassengers: {
          $avg: '$passengers'
        }
      }
    }, 
    {
      $sort: {
        avgPassengers: -1
      }
    }, 
    { $limit: 3 }, 
    {
      $project: {
        avgPassengers: 1,
        _id: 0,
        city: '$_id'
      }
    }
  ]
)
```

```sh
{ "avgPassengers" : 8052.380952380952, "city" : "Abu Dhabi, United Arab Emirates" }
{ "avgPassengers" : 7176.596638655462, "city" : "Dubai, United Arab Emirates" }
{ "avgPassengers" : 7103.333333333333, "city" : "Guangzhou, China" }
```
3.
```
db.airlines.aggregate(
  [
    {
      $match: {
        destCountry: 'Latvia'
      }
    },
    {
      $group: {
        _id: '$destCountry',
        carriers: {
          $addToSet: '$carrier'
        }
      }
    }
  ]
)
```
```sh
{ "_id" : "Latvia", "carriers" : [ "Uzbekistan Airways", "Blue Jet SP Z o o", "JetClub AG" ] }
```

4.
```
db.airlines.aggregate(
  [
    {
      $match: {
        originCountry: 'United States',
        destCountry: {
          $in: ['Greece', 'Italy', 'Spain']
        }
      }
    },
    {
      $group: {
        _id: '$carrier',
        total: {
          $sum: '$passengers'
        }
      }
    },
    {
      $sort: {
        total: -1
      }
    },
    { $limit: 10 },
    { $skip: 3 }
  ]
)
```
```sh
{ "_id" : "Compagnia Aerea Italiana", "total" : 280256 }
{ "_id" : "United Air Lines Inc.", "total" : 229936 }
{ "_id" : "Emirates", "total" : 100903 }
{ "_id" : "Air Europa", "total" : 94968 }
{ "_id" : "Meridiana S.p.A", "total" : 20308 }
{ "_id" : "Norwegian Air Shuttle ASA", "total" : 13344 }
{ "_id" : "VistaJet Limited", "total" : 183 }
```
5.
```
db.airlines.aggregate(
  [
    {
      $match: {
        originCountry: 'United States'
      }
    },
    {
      $group: {
        _id: { state: '$originState', city: '$originCity' },
        totalPassengers: {
          $sum: '$passengers'
        }
      }
    },
    {
      $sort: {
        totalPassengers: -1
      }
    },
    {
      $group: {
        _id: '$_id.state',
        totalPassengers: {
          $first: '$totalPassengers'
        },
        city: {
          $first: '$_id.city'
        }
      }
    },
    {
      $sort: {
        _id: 1
      }
    },
    { $limit: 5 },
    {
      $project: {
        _id: 0,
        totalPassengers: 1,
        location: {
          state: '$_id',
          city: '$city'
        }
      }
    }
  ]
)
```
```sh
{ "totalPassengers" : 760120, "location" : { "state" : "Alabama", "city" : "Birmingham, AL" } }
{ "totalPassengers" : 1472404, "location" : { "state" : "Alaska", "city" : "Anchorage, AK" } }
{ "totalPassengers" : 13152753, "location" : { "state" : "Arizona", "city" : "Phoenix, AZ" } }
{ "totalPassengers" : 571452, "location" : { "state" : "Arkansas", "city" : "Little Rock, AR" } }
{ "totalPassengers" : 23701556, "location" : { "state" : "California", "city" : "Los Angeles, CA" } }
```