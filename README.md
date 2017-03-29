## Design Pattern: Decorator And Facade Pattern
### Decorator Pattern

"The Decorator pattern dynamically adds behaviors and responsibilities to an object without modifying its code. It’s an alternative to subclassing where you modify a class’s behavior by wrapping it with another object."

#### Without Decorator Pattern

```swift
protocol Character {
    func getHealth() -> Int
}

struct John: Character {  
    func getHealth() -> Int {
        return 1
    }
}

struct Orc: Character {
    func getHealth() -> Int {
        return 10
    }
}

struct OrcBarbarian: Character {
    func getHealth() -> Int {
        return 15
    }
}

struct OrcWarlord: Character {
    func getHealth() -> Int {
        return 20
    }
}
```

#### Using Decorator Pattern

```swift
// Core Component
protocol Character {
    func getHealth() -> Int
}

// Concrete Components
struct Orc: Character {
    func getHealth() -> Int {
        return 10
    }
}

struct Elf: Character {
    func getHealth() -> Int {
        return 5
    }
}

// Decorator
protocol CharacterType: Character {
    var base: Character { get }
}

// Concrete Decorators
struct Warlord: CharacterType {
    
    let base: Character
    
    func getHealth() -> Int {
        return 50 + base.getHealth()
    }
     
    // New responsibility
    func battleCry() {
        print("RAWR")   
    }
}

struct Epic: CharacterType {
    
    let base: Character
    
    func getHealth() -> Int {
        return 30 + base.getHealth()
    }
}

let orc = Orc()
let hp = orc.getHealth() // 10

let orcWarlord = Warlord(base: orc)
let hp = orcWarlord.getHealth() // 60

let epicOrcWarlord = Epic(base: orcWarlord)
let hp = epicOrcWarlord.getHealth() // 90

let doubleEpicOrcWarlord = Epic(base: epicOrcWarlord)
let hp = doubleEpicOrcWarlord.getHealth() // 120

let elf = Elf()
let hp = elf.getHealth() // 5

let elfWarlord = Warlord(base: elf)
let hp = elfWarlord.getHealth() // 55
elfWarlord.battleCry() // "RAWR"
```

#### Using Decorator Pattern - Other example
```swift
protocol Coffee {
	func getCost() -> Double
	func getIngredients() -> String
}

class SimpleCoffee: Coffee {
	func getCost() -> Double {
		return 1.0
	}
	
	func getIngredients() -> String {
		return "Coffee"
	}
}

class CoffeeDecorator: Coffee {
	private let decoratedCoffee: Coffee
	private let ingredientSeparator: String = ", "
	
	required init(decoratedCoffee: Coffee) {
		self.decoratedCoffee = decoratedCoffee
	}
	
	func getCost() -> Double {
		return decoratedCoffee.getCost()
	}
	
	func getIngredients() -> String {
		return decoratedCoffee.getIngredients()
	}
}

final class Milk: CoffeeDecorator {
	required init(decoratedCoffee: Coffee) {
		super.init(decoratedCoffee: decoratedCoffee)
	}
	
	override func getCost() -> Double {
		return super.getCost() + 0.5
	}
	
	override func getIngredients() -> String {
		return super.getIngredients() + ingredientSeparator + "Milk"
	}
}

final class Whip: CoffeeDecorator {
	required init(decoratedCoffee: Coffee) {
		super.init(decoratedCoffee: decoratedCoffee)
	}
	
	override func getCost() -> Double {
		return super.getCost() + 0.7
	}
	
	override func getIngredients() -> String {
		return super.getIngredients() + ingredientSeparator + "Whip"
	}
}


var someCoffee: Coffee = SimpleCoffee()
print("Cost : \(someCoffee.getCost()); Ingredients: \(someCoffee.getIngredients())")
someCoffee = Milk(decoratedCoffee: someCoffee)
print("Cost : \(someCoffee.getCost()); Ingredients: \(someCoffee.getIngredients())")
someCoffee = Whip(decoratedCoffee: someCoffee)
print("Cost: \(someCoffee.getCost()); Ingredients: \(someCoffee.getIngredients())")
```

Result:
```
Cost : 1.0; Ingredients: Coffee
Cost : 1.5; Ingredients: Coffee, Milk
Cost : 2.2; Ingredients: Coffee, Milk, Whip
```

### Facade Pattern
##### "The Facade design pattern provides a single interface to a complex subsystem. Instead of exposing the user to a set of classes and their APIs, you only expose one simple unified API."

![facade2](https://cloud.githubusercontent.com/assets/8895399/24435754/6753d390-1461-11e7-9993-9dbe9b900159.png)

##### "LibraryAPI will be exposed to other code, but will hide the HTTPClient and PersistencyManager complexity from the rest of the app."
![design-patterns-facade-uml](https://cloud.githubusercontent.com/assets/8895399/24435696/1326e988-1461-11e7-9eb0-bf5d2f921070.png)

##### So,
```swift
private let persistencyManager: PersistencyManager
private let httpClient: HTTPClient
private let isOnline: Bool
```

```swift
override init() {
  persistencyManager = PersistencyManager()
  httpClient = HTTPClient()
  isOnline = false
 
  super.init()
}
```

```swift
func getAlbums() -> [Album] {
  return persistencyManager.getAlbums()
}
 
func addAlbum(album: Album, index: Int) {
  persistencyManager.addAlbum(album, index: index)
  if isOnline {
    httpClient.postRequest("/api/addAlbum", body: album.description)
  }
}
 
func deleteAlbum(index: Int) {
  persistencyManager.deleteAlbumAtIndex(index)
  if isOnline {
    httpClient.postRequest("/api/deleteAlbum", body: "\(index)")
  }
}
```

##### Reference: 
- https://medium.com/jeremy-codes/decorator-pattern-in-swift-e5fa11ea3c3f
- https://github.com/ochococo/Design-Patterns-In-Swift
- https://www.raywenderlich.com/86477/introducing-ios-design-patterns-in-swift-part-1
