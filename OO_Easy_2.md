## RB120 - Object Oriented Programming --> OO Easy 2

### Exercise 1: Fix the Program - Mailable

Correct the following program so it will work properly. Assume that the Customer and Employee classes have complete implementations; just make the smallest possible change to ensure that objects of both types have access to the `print_address` method.

```ruby
module Mailable
  def print_address
    puts "#{name}"
    puts "#{address}"
    puts "#{city}, #{state} #{zipcode}"
  end
end

class Customer
  attr_reader :name, :address, :city, :state, :zipcode
end

class Employee
  attr_reader :name, :address, :city, :state, :zipcode
end

betty = Customer.new 
bob = Employee.new
betty.print_address
bob.print_address
```

#### Solution

```ruby
module Mailable
  def print_address
    puts "#{name}"
    puts "#{address}"
    puts "#{city}, #{state} #{zipcode}"
  end
end

class Customer
  include Mailable
  attr_reader :name, :address, :city, :state, :zipcode
end

class Employee
  include Mailable
  attr_reader :name, :address, :city, :state, :zipcode
end

betty = Customer.new 
bob = Employee.new
betty.print_address
bob.print_address
```

### Exercise 2: Fix the Program - Drivable

Correct the following program so it will work properly. Assume that the `Car` class has a complete implementation; just make the smallest possible change to ensure that cars have access to the `drive` method.

```ruby
module Drivable
  def self.drive
  end
end

class Car
  include Drivable
end

bobs_car = Car.new
bobs_car.drive
```

#### Solution

```ruby
module Drivable
  def drive
  end
end

class Car
  include Drivable
end

bobs_car = Car.new
bobs_car.drive
```

### Exercise 3: Complete The Program - Houses

Assume you have the following code:

```ruby
class House
  attr_reader :price

  def initialize(price)
    @price = price
  end
end

home1 = House.new(100_000)
home2 = House.new(150_000)
puts "Home 1 is cheaper" if home1 < home2
puts "Home 2 is more expensive" if home2 > home1

# Output
# Home 1 is cheaper
# Home 2 is more expensive
```

Modify the `House` class so that the above program will work. You are permitted to define only one new method in `House`.

#### Solution

```ruby
module Comparable
  def <(other_house)
    self.price < other_house.price ? true : false
  end

  def >(other_house)
    self.price < other_house.price ? false : true
  end

end

class House
  include Comparable
  attr_reader :price

  def initialize(price)
    @price = price
  end

end

home1 = House.new(100_000)
home2 = House.new(150_000)

puts "Home 1 is cheaper" if home1 < home2
puts "Home 2 is more expensive" if home2 > home1
```

#### LS Solution

```ruby
class House
  attr_reader :price
  include Comparable

  def initialize(price)
    @price = price
  end

  def <=>(other_house)
    price <=> other_house.price
  end

end

home1 = House.new(100_000)
home2 = House.new(150_000)

puts "Home 1 is cheaper" if home1 < home2
puts "Home 2 is more expensive" if home2 > home1
```

### Exercise 4: Reverse Ingeneering

Write a class that when the following code is run, it will display the expected output

```ruby
my_data = Transform.new('abc')
puts my_data.uppercase
puts Transform.lowercase('XYZ')

# Expected output
# ABC
# xyz
```

#### Solution

```ruby
class Transform
  def initialize(text)
    @text = text
  end

  def uppercase
    @text.upcase
  end

  def self.lowercase(string)
    string.downcase
  end
end

my_data = Transform.new('abc')
puts my_data.uppercase
puts Transform.lowercase('XYZ')
```

### Exercise 5: What Will This Do?

What will the following code print?

```ruby
class Something
  def initialize
    @data = 'Hello'
  end

  def dupdata
    @data + @data
  end

  def self.dupdata
    'ByeBye'
  end
end

thing = Something.new
puts Something.dupdata
puts thing.dupdata
```

#### Solution

```ruby
# ByeBye
# HelloHello
```

### Exercise 6: Comparing Wallets

Considering the following broken code, modify it so it works. Do not make the amount in the wallet accessible to any method that isn't part of the `Wallet` class.

```ruby
class Wallet
  include Comparable

  def initialize(amount)
    @amount = amount
  end

  def <=>(other_wallet)
    amount <=> other_wallet.amount
  end
end

bills_wallet = Wallet.new(500)
pennys_wallet = Wallet.new(465)
if bills_wallet > pennys_wallet
  puts 'Bill has more money than Penny'
elsif bills_wallet < pennys_wallet
  puts 'Penny has more money than Bill'
else
  puts 'Bill and Penny have the same amount of money.'
end
```

#### Solution

```ruby
class Wallet
  include Comparable

  def initialize(amount)
    @amount = amount
  end

  def <=>(other_wallet)
    amount <=> other_wallet.amount
  end

  protected 

  attr_reader :amount
end

bills_wallet = Wallet.new(500)
pennys_wallet = Wallet.new(465)

if bills_wallet > pennys_wallet
  puts 'Bill has more money than Penny'
elsif bills_wallet < pennys_wallet
  puts 'Penny has more money than Bill'
else
  puts 'Bill and Penny have the same amount of money.'
end
```

### Exercise 7: Pet Shelter

Consider the following code:

```ruby
butterscotch = Pet.new('cat', 'Butterscotch')
pudding      = Pet.new('cat', 'Pudding')
darwin       = Pet.new('bearded dragon', 'Darwin')
kennedy      = Pet.new('dog', 'Kennedy')
sweetie      = Pet.new('parakeet', 'Sweetie Pie')
molly        = Pet.new('dog', 'Molly')
chester      = Pet.new('fish', 'Chester')

phanson = Owner.new('P Hanson')
bholmes = Owner.new('B Holmes')

shelter = Shelter.new
shelter.adopt(phanson, butterscotch)
shelter.adopt(phanson, pudding)
shelter.adopt(phanson, darwin)
shelter.adopt(bholmes, kennedy)
shelter.adopt(bholmes, sweetie)
shelter.adopt(bholmes, molly)
shelter.adopt(bholmes, chester)
shelter.print_adoptions
puts "#{phanson.name} has #{phanson.number_of_pets} adopted pets."
puts "#{bholmes.name} has #{bholmes.number_of_pets} adopted pets."
```

Write the classes and methods that will be necessary to make this code run, and print the following output (the order of the output does not matter, so long as all of the information is presented):

```ruby
# P Hanson has adopted the following pets:
# a cat named Butterscotch
# a cat named Pudding
# a bearded dragon named Darwin

# B Holmes has adopted the following pets:
# a dog named Molly
# a parakeet named Sweetie Pie
# a dog named Kennedy
# a fish named Chester

# P Hanson has 3 adopted pets.
# B Holmes has 4 adopted pets.
```

#### Solution

```ruby
class Pet
  attr_reader :type, :name
  
  def initialize(type, name)
    @type = type
    @name = name
  end
end

class Owner
  attr_reader :name, :pets
  
  def initialize(name)
    @name = name
    @pets = []
  end
  
  def new_pet(pet)
    @pets << pet
  end
  
  def number_of_pets
    pets.size
  end
end

class Shelter
  attr_reader :owners
  
  def initialize
    @owners = []
  end
  
  def adopt(owner, pet)
    owner.new_pet(pet)
    @owners << owner unless @owners.include?(owner)
  end
  
  def print_adoptions
    self.owners.each do |owner|
      puts "#{owner.name} has adopted the following #{Pet.name.downcase}s"
      puts owner.pets.map { |pet| "a #{pet.type} named #{pet.name}"}
      puts
    end
  end
end

butterscotch = Pet.new('cat', 'Butterscotch')
pudding      = Pet.new('cat', 'Pudding')
darwin       = Pet.new('bearded dragon', 'Darwin')
kennedy      = Pet.new('dog', 'Kennedy')
sweetie      = Pet.new('parakeet', 'Sweetie Pie')
molly        = Pet.new('dog', 'Molly')
chester      = Pet.new('fish', 'Chester')

phanson = Owner.new('P Hanson')
bholmes = Owner.new('B Holmes')

shelter = Shelter.new

shelter.adopt(phanson, butterscotch)
shelter.adopt(phanson, pudding)
shelter.adopt(phanson, darwin)
shelter.adopt(bholmes, kennedy)
shelter.adopt(bholmes, sweetie)
shelter.adopt(bholmes, molly)
shelter.adopt(bholmes, chester)

shelter.print_adoptions

puts "#{phanson.name} has #{phanson.number_of_pets} adopted pets."
puts "#{bholmes.name} has #{bholmes.number_of_pets} adopted pets."
```

#### Further exploration
Coded in collaboration with Nicolas Camus (16_Jun_21)

#### Notes:

- We opted for updating the Shelter's homeless_pets instance variable every time a new instance of Pet is initialized (any "newborn" pet, is automatically added to a list of pets available for adoption in a shelter).
- To achieve that, an specific Shelter object has to be passed as argument at the time of instantiating a Pet object.
- Then, every time a Pet object is adopted by an Owner, that pet gets deleted from the homeless_pets list.
- This way we can have multiple shelter and the program can easily be escalated and used in different shelters (imagine the different shelters in a city)

```ruby
class Pet
  attr_reader :type, :name

  def initialize(type, name, shelter)
    @type = type
    @name = name
    shelter.homeless_pets << self
  end

  def to_s
    "a #{type} named #{name}"
  end
end

class Owner
  attr_reader :name, :pets

  def initialize(name)
    @name = name
    @pets = []
  end

  def new_pet(pet)
    @pets << pet
  end

  def number_of_pets
    pets.size
  end
end

class Shelter
  attr_reader :owners, :homeless_pets

  def initialize
    @owners = []
    @homeless_pets = []
  end

  def adopt(owner, pet)    
    owner.new_pet(pet)
    @owners << owner unless @owners.include?(owner)
    @homeless_pets.delete(pet)
  end

  def print_adoptions
    self.owners.each do |owner|
      puts "#{owner.name} has adopted the following #{Pet.name.downcase}s:"
      puts owner.pets
      puts
    end

    puts "The Animal Shelter has the following unadopted pets:"
    puts homeless_pets
    puts
  end
end

shelter = Shelter.new

butterscotch = Pet.new('cat', 'Butterscotch', shelter)
pudding      = Pet.new('cat', 'Pudding', shelter)
darwin       = Pet.new('bearded dragon', 'Darwin', shelter)
kennedy      = Pet.new('dog', 'Kennedy', shelter)
sweetie      = Pet.new('parakeet', 'Sweetie Pie', shelter)
molly        = Pet.new('dog', 'Molly', shelter)
chester      = Pet.new('fish', 'Chester', shelter)
puppy        = Pet.new('turtle', 'Turt', shelter)
fluffy       = Pet.new('cat', 'Fluffy', shelter)
piolin       = Pet.new('bird', 'Piolin', shelter)
silvestre    = Pet.new('cat', 'Silvestre', shelter)

phanson = Owner.new('P Hanson')
bholmes = Owner.new('B Holmes')
ncamus = Owner.new('N Camus')
alobato = Owner.new('A Lobato')

shelter.adopt(phanson, butterscotch)
shelter.adopt(phanson, pudding)
shelter.adopt(phanson, darwin)
shelter.adopt(bholmes, kennedy)
shelter.adopt(bholmes, sweetie)
shelter.adopt(bholmes, molly)
shelter.adopt(bholmes, chester)
shelter.adopt(ncamus, silvestre)
shelter.adopt(ncamus, piolin)
shelter.adopt(alobato, fluffy)

shelter.print_adoptions

puts "#{phanson.name} has #{phanson.number_of_pets} adopted pets."
puts "#{bholmes.name} has #{bholmes.number_of_pets} adopted pets."
puts "#{ncamus.name} has #{ncamus.number_of_pets} adopted pets."
puts "#{alobato.name} has #{alobato.number_of_pets} adopted pets."
```

### Exercise 8: Fix The Program - Expander

What is wrong with the following code? What fix(es) would you make?

```ruby
class Expander
  def initialize(string)
    @string = string
  end

  def to_s
    self.expand(3)
  end

  private

  def expand(n)
    @string * n
  end
end

expander = Expander.new('xyz')
puts expander
```

#### Solution

```ruby
  def to_s
    expand(3)
  end
```

### Exercise 9: Moving

Write only one new method to modify this code, so the instantiations and method calls at the bottom work:

```ruby
class Person
  attr_reader :name

  def initialize(name)
    @name = name
  end

  private

  def gait
    "strolls"
  end
end

class Cat
  attr_reader :name

  def initialize(name)
    @name = name
  end

  private

  def gait
    "saunters"
  end
end

class Cheetah
  attr_reader :name

  def initialize(name)
    @name = name
  end

  private

  def gait
    "runs"
  end
end

mike = Person.new("Mike")
mike.walk
# => "Mike strolls forward"

kitty = Cat.new("Kitty")
kitty.walk
# => "Kitty saunters forward"

flash = Cheetah.new("Flash")
flash.walk
# => "Flash runs forward"
```

#### Solution

```ruby
module Walkable
  def walk
    puts "#{name} #{gait} forward"
  end
end

class Person
  attr_reader :name

  include Walkable

  def initialize(name)
    @name = name
  end

  private

  def gait
    "strolls"
  end
end

class Cat
  attr_reader :name

  include Walkable

  def initialize(name)
    @name = name
  end

  private

  def gait
    "saunters"
  end
end

class Cheetah
  attr_reader :name

  include Walkable

  def initialize(name)
    @name = name
  end

  private

  def gait
    "runs"
  end
end

mike = Person.new("Mike")
mike.walk
# => "Mike strolls forward"

kitty = Cat.new("Kitty")
kitty.walk
# => "Kitty saunters forward"

flash = Cheetah.new("Flash")
flash.walk
# => "Flash runs forward"
```

### Exercise 9: Nobility

Now that we have a `Walkable` module, we are given a new challenge. Apparently some of our users are nobility, and the regular way of walking simply isn't good enough. Nobility need to strut.

We need a new class `Noble` that shows the title and name when `walk` is called:

```ruby
byron = Noble.new("Byron", "Lord")
p byron.walk
# => "Lord Byron struts forward"
```

We must have access to both `name` and `title` because they are needed for other purposes that we aren't showing here.

```ruby
byron.name
=> "Byron"
byron.title
=> "Lord"
```

#### Solution

```ruby
module Walkable
  def walk
    "#{self} #{gait} forward"
  end
end

class Person
  attr_reader :name

  include Walkable

  def initialize(name)
    @name = name
  end

  def to_s
    name
  end

  private

  def gait
    "strolls"
  end
end

class Cat
  attr_reader :name

  include Walkable

  def initialize(name)
    @name = name
  end

  def to_s
    name
  end

  private

  def gait
    "saunters"
  end
end

class Cheetah
  attr_reader :name

  include Walkable

  def initialize(name)
    @name = name
  end

  def to_s
    name
  end

  private

  def gait
    "runs"
  end
end

class Noble
  attr_reader :name, :title

  include Walkable

  def initialize(name, title)
    @name = name
    @title = title
  end

  def to_s
    "#{title} #{name}"
  end

  def gait
    "struts"
  end
end

mike = Person.new("Mike")
puts mike.walk
# => "Mike strolls forward"

kitty = Cat.new("Kitty")
puts kitty.walk
# => "Kitty saunters forward"

flash = Cheetah.new("Flash")
puts flash.walk
# => "Flash runs forward"

byron = Noble.new("Byron", "Lord")
puts byron.walk
# => "Lord Byron struts forward"
```

#### Further Exploration

This exercise can be solved in a similar manner by using inheritance; a Noble is a Person, and a Cheetah is a Cat, and both Persons and Cats are Animals. What changes would you need to make to this program to establish these relationships and eliminate the two duplicated `#to_s` methods?

Is to_s the best way to provide the name and title functionality we needed for this exercise? Might it be better to create either a different name method (or say a new full_name method) that automatically accesses @title and @name? There are tradeoffs with each choice -- they are worth considering.

#### Solution 1

```ruby
class Animal

  def walk
    "#{self} #{gait} forward"
  end

  def to_s
    name
  end

end

class Person < Animal
  attr_reader :name

  def initialize(name)
    @name = name
  end

  private

  def gait
    "strolls"
  end
end

class Cat < Animal
  attr_reader :name

  def initialize(name)
    @name = name
  end

  private

  def gait
    "saunters"
  end
end

class Cheetah < Cat
  attr_reader :name

  def initialize(name)
    @name = name
  end

  private

  def gait
    "runs"
  end
end

class Noble < Person
  attr_reader :name, :title

  def initialize(name, title)
    @name = name
    @title = title
  end

  def to_s
    "#{title} #{name}"
  end

  def gait
    "struts"
  end
end

mike = Person.new("Mike")
puts mike.walk
# => "Mike strolls forward"

kitty = Cat.new("Kitty")
puts kitty.walk
# => "Kitty saunters forward"

flash = Cheetah.new("Flash")
puts flash.walk
# => "Flash runs forward"

byron = Noble.new("Byron", "Lord")
puts byron.walk
# => "Lord Byron struts forward"
```

#### Solution 2

```ruby
module Walkable
  def walk
    "#{@name} #{gait} forward"
  end
end

class Person
  attr_reader :name

  include Walkable

  def initialize(name)
    @name = name
  end

  private

  def gait
    "strolls"
  end
end

class Cat
  attr_reader :name

  include Walkable

  def initialize(name)
    @name = name
  end

  private

  def gait
    "saunters"
  end
end

class Cheetah < Cat
  attr_reader :name

  include Walkable

  def initialize(name)
    @name = name
  end

  private

  def gait
    "runs"
  end
end

class Noble < Person
  attr_reader :name, :title

  include Walkable

  def initialize(name, title)
    @name = name
    @title = title
  end

  def walk
    puts "#{title} #{name} #{gait} forward"
  end

  private

  def gait
    "struts"
  end
end

mike = Person.new("Mike")
puts mike.walk
# => "Mike strolls forward"

kitty = Cat.new("Kitty")
puts kitty.walk
# => "Kitty saunters forward"

flash = Cheetah.new("Flash")
puts flash.walk
# => "Flash runs forward"

byron = Noble.new("Byron", "Lord")
puts byron.walk
# => "Lord Byron struts forward"
```
