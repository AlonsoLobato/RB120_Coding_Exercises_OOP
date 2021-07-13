## RB120 - Object Oriented Programming --> OO Debuging

### Exercise 1: Community Library

On line 42 of our code, we intend to display information regarding the books currently checked in to our community library. Instead, an exception is raised. Determine what caused this error and fix the code so that the data is displayed as expected.

```ruby
class Library
  attr_accessor :address, :phone, :books

  def initialize(address, phone)
    @address = address
    @phone = phone
    @books = []
  end

  def check_in(book)
    books.push(book)
  end
end

class Book
  attr_accessor :title, :author, :isbn

  def initialize(title, author, isbn)
    @title = title
    @author = author
    @isbn = isbn
  end

  def display_data
    puts "---------------"
    puts "Title: #{title}"
    puts "Author: #{author}"
    puts "ISBN: #{isbn}"
    puts "---------------"
  end
end

community_library = Library.new('123 Main St.', '555-232-5652')
learn_to_program = Book.new('Learn to Program', 'Chris Pine', '978-1934356364')
little_women = Book.new('Little Women', 'Louisa May Alcott', '978-1420951080')
wrinkle_in_time = Book.new('A Wrinkle in Time', 'Madeleine L\'Engle', '978-0312367541')

community_library.check_in(learn_to_program)
community_library.check_in(little_women)
community_library.check_in(wrinkle_in_time)

community_library.books.display_data
```

#### Solution

The error that line 42 is raising is caused because a method define for a `Book` class object, `display_data`, is been invoked in an `Array` class object.

The `books` ivar defined for `Library` objects references to an array of books, and the `display_data` method is being call on that array, which has not such a method defined for its use.

To fix the problem we can do two things:

- Call the `display_data` instance method on each book object (or on one or several individual book objects) from the client code/main flow of execution

  ```ruby 
  community_library.books.each(&:display_data)
  ```

- Or define an specific instance method for library objects (inside the `Library` class) that deals with each book individually

  ```ruby
  class Library
    # other code omitted
    
  	def display_books
      books.each(&:display_data)
    end
  end
  ```

- In any case, the expected output would be:

  ```ruby
  ---------------
  Title: Learn to Program
  Author: Chris Pine
  ISBN: 978-1934356364
  ---------------
  ---------------
  Title: Little Women
  Author: Louisa May Alcott
  ISBN: 978-1420951080
  ---------------
  ---------------
  Title: A Wrinkle in Time
  Author: Madeleine L'Engle
  ISBN: 978-0312367541
  ---------------
  ```

  

### Exercise 2: Animal Kingdom

The code below raises an exception. Examine the error message and alter the code so that it runs without error.

```ruby
class Animal
  def initialize(diet, superpower)
    @diet = diet
    @superpower = superpower
  end

  def move
    puts "I'm moving!"
  end

  def superpower
    puts "I can #{@superpower}!"
  end
end

class Fish < Animal
  def move
    puts "I'm swimming!"
  end
end

class Bird < Animal
end

class FlightlessBird < Bird
  def initialize(diet, superpower)
    super
  end

  def move
    puts "I'm running!"
  end
end

class SongBird < Bird
  def initialize(diet, superpower, song)
    super
    @song = song
  end

  def move
    puts "I'm flying!"
  end
end

# Examples

unicornfish = Fish.new(:herbivore, 'breathe underwater')
penguin = FlightlessBird.new(:carnivore, 'drink sea water')
robin = SongBird.new(:omnivore, 'sing', 'chirp chirrr chirp chirp chirrrr')
```

The error risen is related to the number of arguments each object takes at the time of instantiation. We can see how there is a superclass `Animal` wich defines the `initialize` constructor method and some other classes that inherit, directly or indirectly from that superclass and override the `initialize` in different ways.

When we override a method in a subclass and use the keyword `super` to pass the execution to the parent, we need to pass the required arguments to the method define earlier in the method lookup path using `super`. 

In this example, the `initialize` method defined in the subclass `SongBird` overrides the method with same name defined in the parent class `Bird`, which in turn override the same method define in its parent class `Animal`. We need to modify the way `super` passes arguments to the parents as required in the method definition in the superclasses.

```ruby
class Animal
	# other code omitted
end

class Fish < Animal
	# other code omitted
end

class Bird < Animal
end

class FlightlessBird < Bird
  def initialize(diet, superpower)              # (2)
    super
  end
  
	# other code omitted
end

class SongBird < Bird
  def initialize(diet, superpower, song)
    super(diet, superpower)                     # (1)
    @song = song
  end

	# other code omitted
end
```

1. `super` needs to be defined to take the required arguments by `Animal`'s `initialize` method (two arguments, instead of three).
2. in this case, we don't need to specify which arguments need to be passed as `super` passess all arguments up to the parent class by default and in this case both, the child and the parent's `initialize` method take two arguments.
   Actually, the `initialize` method of the `FlightlessBird` class isn't even necesary as it does not modify (override) the parent method at all, so we can delete it and Ruby will find the correct method in the method lookup path.



### Exercise 3: Wish You Were Here

On lines 37 and 38 of our code, we can see that `grace` and `ada` are located at the same coordinates. So why does line 39 output `false`? Fix the code to produce the expected output.

```ruby
class Person
  attr_reader :name
  attr_accessor :location

  def initialize(name)
    @name = name
  end

  def teleport_to(latitude, longitude)
    @location = GeoLocation.new(latitude, longitude)
  end
end

class GeoLocation
  attr_reader :latitude, :longitude

  def initialize(latitude, longitude)
    @latitude = latitude
    @longitude = longitude
  end

  def to_s
    "(#{latitude}, #{longitude})"
  end
end

# Example

ada = Person.new('Ada')
ada.location = GeoLocation.new(53.477, -2.236)

grace = Person.new('Grace')
grace.location = GeoLocation.new(-33.89, 151.277)

ada.teleport_to(-33.89, 151.277)

puts ada.location                   # (-33.89, 151.277)
puts grace.location                 # (-33.89, 151.277)
puts ada.location == grace.location # expected: true
                                    # actual: false
```

One more time, there are several options to solve this problem. 

The important fact is that `grace.location` and `ada.location` both return objects of the `Geolocation` class. The `==` method is used as defined in the `BasicObject` class, which compares if two objects are the same (same obj id), which in this case isn't true, as oppose to if two objects hold the same value, which in this case is true.

So, to compare the values that the two objects point to we have two main ways to go:

- We can call `to_s` on the objects before comparing them in the main flow of execution (`to_s` method is defined for `Geolocation` objects) and then compare them:

  ```ruby
  # All classes and methods omitted
  
  puts ada.location                   
  # (-33.89, 151.277)
  puts grace.location                 
  # (-33.89, 151.277)
  puts ada.location.to_s == grace.location.to_s 
  # true
  
  ```

- We can define our custom `==` method in the `Geolocation` class and give clear instructions on how to compare two objects of the class:

  ```ruby
  class GeoLocation
    attr_reader :latitude, :longitude
  
    def initialize(latitude, longitude)
      @latitude = latitude
      @longitude = longitude
    end
  
    def to_s
      "(#{latitude}, #{longitude})"
    end
  
    def ==(other_person)
      latitude == other_person.latitude && longitude == other_person.longitude
    end
  end
  ```

  ```ruby
  class GeoLocation
    attr_reader :latitude, :longitude
  
    def initialize(latitude, longitude)
      @latitude = latitude
      @longitude = longitude
    end
  
    def to_s
      "(#{latitude}, #{longitude})"
    end
  
    def ==(other_person)
      to_s == other_person.to_s
    end
  end
  ```

- We can also approach this problem by creating our own custom definition of `location` rather than using `attr_accessor`. Within this custom definition, we'll invoke `GeoLocation`'s `to_s` method on `@location`.

  ```ruby
  class Person
    attr_reader :name
    attr_writer :location
  
    def initialize(name)
      @name = name
    end
  
    def teleport_to(latitude, longitude)
      @location = GeoLocation.new(latitude, longitude)
    end
    
    def location
      @location.to_s
    end
  end
  
  # the Geolocation class remain as originally given
  ```

  

### Exercise 4: Employee Management

We have written some code for a simple employee management system. Each employee must have a unique serial number. However, when we are testing our program, an exception is raised. Fix the code so that the program works as expected without error.

```ruby
class EmployeeManagementSystem
  attr_reader :employer

  def initialize(employer)
    @employer = employer
    @employees = []
  end

  def add(employee)
    if exists?(employee)
      puts "Employee serial number is already in the system."
    else
      employees.push(employee)
      puts "Employee added."
    end
  end

  alias_method :<<, :add

  def remove(employee)
    if !exists?(employee)
      puts "Employee serial number is not in the system."
    else
      employees.delete(employee)
      puts "Employee deleted."
    end
  end

  def exists?(employee)
    employees.any? { |e| e == employee }
  end

  def display_all_employees
    puts "#{employer} Employees: "

    employees.each do |employee|
      puts ""
      puts employee.to_s
    end
  end

  private

  attr_accessor :employees
end

class Employee
  attr_reader :name

  def initialize(name, serial_number)
    @name = name
    @serial_number = serial_number
  end

  def ==(other)
    serial_number == other.serial_number
  end

  def to_s
    "Name: #{name}\n" +
    "Serial No: #{abbreviated_serial_number}"
  end

  private

  attr_reader :serial_number

  def abbreviated_serial_number
    serial_number[-4..-1]
  end
end

# Example

miller_contracting = EmployeeManagementSystem.new('Miller Contracting')

becca = Employee.new('Becca', '232-4437-1932')
raul = Employee.new('Raul', '399-1007-4242')
natasha = Employee.new('Natasha', '399-1007-4242')

miller_contracting << becca     # => Employee added.
miller_contracting << raul      # => Employee added.
miller_contracting << raul      # => Employee serial number is already in the system.
miller_contracting << natasha   # => Employee serial number is already in the system.
miller_contracting.remove(raul) # => Employee deleted.
miller_contracting.add(natasha) # => Employee added.

miller_contracting.display_all_employees
```

The problem in this code is the way some of the methods in the `Employee` class are private: the getter method for `serial_number` and the `abbreviated_serial_number` instance method. On paper it makes sense to keep these two methods unaccessible from outside the class and the `@serial_number` ivar holds sensitive information that we want to protect. 

The problem is that private methods cannot be invoked with an explicit caller, even inside of their own class. On line 56, in `Employee#==`, we do invoke `serial_number` with an explicit caller (`other`) and this causes the error.

In order to make this work, we can make `serial_number` a *protected* method instead. Recall that from outside the class, protected methods work just like private methods. From inside the class, however, protected methods are accessible and may be invoked with an explicit caller.

```ruby
class Employee
  # rest of code omitted
  
  protected
  
  attr_reader :serial_number
  
  private
  
  def abbreviated_serial_number
    serial_number[-4..-1]
  end
end
```



### Exercise 5: Files

You started writing a very basic class for handling files. However, when you begin to write some simple test code, you get a `NameError`. The error message complains of an `uninitialized constant File::FORMAT`.

What is the problem and what are possible ways to fix it?

```ruby
class File
  attr_accessor :name, :byte_content

  def initialize(name)
    @name = name
  end

  alias_method :read,  :byte_content
  alias_method :write, :byte_content=

  def copy(target_file_name)
    target_file = self.class.new(target_file_name)
    target_file.write(read)

    target_file
  end

  def to_s
    "#{name}.#{FORMAT}"
  end
end

class MarkdownFile < File
  FORMAT = :md
end

class VectorGraphicsFile < File
  FORMAT = :svg
end

class MP3File < File
  FORMAT = :mp3
end

# Test

blog_post = MarkdownFile.new('Adventures_in_OOP_Land')
blog_post.write('Content will be added soon!'.bytes)

copy_of_blog_post = blog_post.copy('Same_Adventures_in_OOP_Land')

puts copy_of_blog_post.is_a? MarkdownFile     # true
puts copy_of_blog_post.read == blog_post.read # true

puts blog_post
```

The problem is causes when Ruby try to access `FORMAT` in the lexical scope where this constant is invoked. When called from a `File` class object, Ruby looks for it in the class and its ancestors, but don't find it at all and therefore the error is rised.

To fix the problem we can tell Ruby, via the namespacing operator `::`, in which scope to look for a constant, method, etc.

```rubby
class File
	# code omitted

  def to_s
    "#{name}.#{self.class::FORMAT}"
  end
end

# more code omitted
```



### Exercise 6: Sorting Distances

When attempting to sort an array of various lengths, we are surprised to see that an `ArgumentError` is raised. Make the necessary changes to our code so that the various lengths can be properly sorted and line 62 produces the expected output.

```ruby
class Length
  attr_reader :value, :unit

  def initialize(value, unit)
    @value = value
    @unit = unit
  end

  def as_kilometers
    convert_to(:km, { km: 1, mi: 0.6213711, nmi: 0.539957 })
  end

  def as_miles
    convert_to(:mi, { km: 1.609344, mi: 1, nmi: 0.8689762419 })
  end

  def as_nautical_miles
    convert_to(:nmi, { km: 1.8519993, mi: 1.15078, nmi: 1 })
  end

  def ==(other)
    case unit
    when :km  then value == other.as_kilometers.value
    when :mi  then value == other.as_miles.value
    when :nmi then value == other.as_nautical_miles.value
    end
  end

  def <(other)
    case unit
    when :km  then value < other.as_kilometers.value
    when :mi  then value < other.as_miles.value
    when :nmi then value < other.as_nautical_miles.value
    end
  end

  def <=(other)
    self < other || self == other
  end

  def >(other)
    !(self <= other)
  end

  def >=(other)
    self > other || self == other
  end

  def to_s
    "#{value} #{unit}"
  end

  private

  def convert_to(target_unit, conversion_factors)
    Length.new((value / conversion_factors[unit]).round(4), target_unit)
  end
end

# Example

puts [Length.new(1, :mi), Length.new(1, :nmi), Length.new(1, :km)].sort
# => comparison of Length with Length failed (ArgumentError)
# expected output:
# 1 km
# 1 mi
# 1 nmi
```

The `Array#sort` method uses the `<=>` method to compare and sort elements in the array. For that to be possible, the elements of the array must have access to the `<=>` method. In this case the elements of the array are `Length` objects and the `<=>` method isn't defined for objects of that class.

The solution implies the definition of that `<=>` method in the `Length` class; but we need to be aware that the conversion between units must be done in order for `<=>` to work as expected:

```ruby
class Length
  # code omitted
  
  def <=>(other)
    case unit
    when :km  then value <=> other.as_kilometers.value
    when :mi  then value <=> other.as_miles.value
    when :nmi then value <=> other.as_nautical_miles.value
    end
  end
end
```



### Exercise 7: Bank Balance

We created a simple `BankAccount` class with overdraft protection, that does not allow a withdrawal greater than the amount of the current balance. We wrote some example code to test our program. However, we are surprised by what we see when we test its behavior. Why are we seeing this unexpected output? Make changes to the code so that we see the appropriate behavior.

```ruby
class BankAccount
  attr_reader :balance

  def initialize(account_number, client)
    @account_number = account_number
    @client = client
    @balance = 0
  end

  def deposit(amount)
    if amount > 0
      self.balance += amount
      "$#{amount} deposited. Total balance is $#{balance}."
    else
      "Invalid. Enter a positive amount."
    end
  end

  def withdraw(amount)
    if amount > 0
      success = (self.balance -= amount)
    else
      success = false
    end

    if success
      "$#{amount} withdrawn. Total balance is $#{balance}."
    else
      "Invalid. Enter positive amount less than or equal to current balance ($#{balance})."
    end
  end

  def balance=(new_balance)
    if valid_transaction?(new_balance)
      @balance = new_balance
      true
    else
      false
    end
  end

  def valid_transaction?(new_balance)
    new_balance >= 0
  end
end

# Example

account = BankAccount.new('5538898', 'Genevieve')

                          # Expected output:
p account.balance         # => 0
p account.deposit(50)     # => $50 deposited. Total balance is $50.
p account.balance         # => 50
p account.withdraw(80)    # => Invalid. Enter positive amount less than or equal to current balance ($50).
                          # Actual output: $80 withdrawn. Total balance is $50.
p account.balance         # => 50
```

In Ruby, setter methods *always* return the argument that was passed in, even when you add an explicit `return` statement. Our `balance=` method will therefore always return its argument, irrespective of whether or not the instance variable `@balance` is re-assigned. Because of this behavior, the invocation of `balance=` on line 21 of the original code will have a truthy return value even when our setter method does not re-assign `@balance`; it will never return `false`.

A solution to not allow to withdraw more money than currently available in the balance can be implemented in the `withdraw` method. The `if` branch of the method only checks if the withdrawing `amount` is larger than `0` where it could also check if it is possible to withdraw that amount according to what's currently available.

We sort this out by adding a second condition to the `if` branch, using the `valid_transaction` instance method defined further down in the class.

With this change, the `balance=` setter method, can be simplified to a reassignation of current balance to a new balance.

```ruby
class BankAccount
   # code omitted

  def withdraw(amount)
    if amount > 0 && valid_transaction?(balance - amount)
      self.balance -= amount
      "$#{amount} withdrawn. Total balance is $#{balance}."
    else
      "Invalid. Enter positive amount less than or equal to current balance ($#{balance})."
    end
  end

  def balance=(new_balance)
    @balance = new_balance
  end

  # code omitted
end
```



### Exercise 8: Task Manager

Valentina is using a new task manager program she wrote. When interacting with her task manager, an error is raised that surprises her. Can you find the bug and fix it?

```ruby
class TaskManager
  attr_reader :owner
  attr_accessor :tasks

  def initialize(owner)
    @owner = owner
    @tasks = []
  end

  def add_task(name, priority=:normal)
    task = Task.new(name, priority)
    tasks.push(task)
  end

  def complete_task(task_name)
    completed_task = nil

    tasks.each do |task|
      completed_task = task if task.name == task_name
    end

    if completed_task
      tasks.delete(completed_task)
      puts "Task '#{completed_task.name}' complete! Removed from list."
    else
      puts "Task not found."
    end
  end

  def display_all_tasks
    display(tasks)
  end

  def display_high_priority_tasks
    tasks = tasks.select do |task|
      task.priority == :high
    end

    display(tasks)
  end

  private

  def display(tasks)
    puts "--------"
    tasks.each do |task|
      puts task
    end
    puts "--------"
  end
end

class Task
  attr_accessor :name, :priority

  def initialize(name, priority=:normal)
    @name = name
    @priority = priority
  end

  def to_s
    "[" + sprintf("%-6s", priority) + "] #{name}"
  end
end

valentinas_tasks = TaskManager.new('Valentina')

valentinas_tasks.add_task('pay bills', :high)
valentinas_tasks.add_task('read OOP book')
valentinas_tasks.add_task('practice Ruby')
valentinas_tasks.add_task('run 5k', :low)

valentinas_tasks.complete_task('read OOP book')

valentinas_tasks.display_all_tasks
valentinas_tasks.display_high_priority_tasks
```

The error this code throws is related to `select` method being called on `nil` on line 35.

The reason why `tasks` points to `nil` in the `display_high_priority_tasks` method is that Ruby thinks we are initializing a local variable `tasks` to the return value of calling `select` on that very same local variable. It is a little convoluted, but we are initializing a local variable `tasks`, and on the same line we reference it via `tasks.select`, before it has been assigned a value, so it points to `nil`. Invoking the `select` method on `nil` caused the error we see.  

To correct this error we have 3 main solutions:

- We can dissambiguate by using `@tasks.select` or `self.tasks.select` on the left part of the assignment on line 35. That way Ruby knows that the `select` method is called on the isntance variable `tasks` or on the getter method for that ivar, which points to an array of tasks.

  ```ruby
  def display_high_priority_tasks
    tasks = self.tasks.select do |task|
      task.priority == :high
    end
  
    display(tasks)
  end
  
  # OR
  
  def display_high_priority_tasks
    tasks = @tasks.select do |task|
      task.priority == :high
    end
  
    display(tasks)
  end
  ```

- We can use a completely different variable name, so Ruby don't ever think that we mean to call the getter method. Declaring a completely different variable, `high_priority_tasks` for example, to refer to the return value of the `tasks.select` method call. This also disambiguates which object is being referred to by `tasks` in that method call and allows the program to function as expected. Perhaps this this the best of the solutions.

  ```ruby
  def display_high_priority_tasks
    high_priority_tasks = tasks.select do |task|
      task.priority == :high
    end
  
    display(high_priority_tasks)
  end
  ```

- A final option (that will not have the desired results) is to use the instance variable `@tasks` to hold the return value of the `tasks.select` method call. This decision would result in reassignment of the instance variable to only the high priority tasks, disregarding all other contents - a destructive operation and not advised.

  ```ruby
  def display_high_priority_tasks
    @tasks = tasks.select do |task|
      task.priority == :high
    end
  
    display(@tasks)
  end
  
  #OR
  
  def display_high_priority_tasks
    self.tasks = tasks.select do |task|
      task.priority == :high
    end
  
    display(@tasks)
  end
  ```

  

### Exercise 9: You've Got Mail!

Can you decipher and fix the error that the following code produces?

```ruby
class Mail
  def to_s
    "#{self.class}"
  end
end

class Email < Mail
  attr_accessor :subject, :body

  def initialize(subject, body)
    @subject = subject
    @body = body
  end
end

class Postcard < Mail
  attr_reader :text

  def initialize(text)
    @text = text
  end
end

module Mailing
  def receive(mail, sender)
    mailbox << mail unless reject?(sender)
  end

  # Change if there are sources you want to block.
  def reject?(sender)
    false
  end

  def send(destination, mail)
    "Sending #{mail} from #{name} to: #{destination}"
    # Omitting the actual sending.
  end
end

class CommunicationsProvider
  attr_reader :name, :account_number

  def initialize(name, account_number=nil)
    @name = name
    @account_number = account_number
  end
end

class EmailService < CommunicationsProvider
  include Mailing

  attr_accessor :email_address, :mailbox

  def initialize(name, account_number, email_address)
    super(name, account_number)
    @email_address = email_address
    @mailbox = []
  end

  def empty_inbox
    self.mailbox = []
  end
end

class TelephoneService < CommunicationsProvider
  def initialize(name, account_number, phone_number)
    super(name, account_number)
    @phone_number = phone_number
  end
end

class PostalService < CommunicationsProvider
  attr_accessor :street_address, :mailbox

  def initialize(name, street_address)
    super(name)
    @street_address = street_address
    @mailbox = []
  end

  def change_address(new_address)
    self.street_address = new_address
  end
end

rafaels_email_account = EmailService.new('Rafael', 111, 'Rafael@example.com')
johns_phone_service   = TelephoneService.new('John', 122, '555-232-1121')
johns_postal_service  = PostalService.new('John', '47 Sunshine Ave.')
ellens_postal_service = PostalService.new('Ellen', '860 Blackbird Ln.')

puts johns_postal_service.send(ellens_postal_service.street_address, Postcard.new('Greetings from Silicon Valley!'))
# => undefined method `860 Blackbird Ln.' for #<PostalService:0x00005571b4aaebe8> (NoMethodError)
```

The problem in this code is that we're attempting to call a `#send` instance method that is defined in the `Mailing` module on a `PostalService` object, but `PostalService` has no access to the `Mailing` module. Also `PostalService`'s superclass `CommunicatorProvider` does not have access to the `send` method.

So to solve this we can mix in the `Mailing` module into one of the two classes mentioned above, being the first a better option, as other subclasses that inherit from `CommunicatorProvider` (specially `TelephoneService`) don't need to have access to the method defined in `Mailing`.

```ruby
# rest of code omitted

class PostalService < CommunicationsProvider
  include Mailing 
  
  attr_accessor :street_address, :mailbox

  def initialize(name, street_address)
    super(name)
    @street_address = street_address
    @mailbox = []
  end

  def change_address(new_address)
    self.street_address = new_address
  end
end

# rest of code omitted
```

Also, although the `send` method does not exist in this code, it does exist in the `Object` class, so we are accidentally overriding it. To avoid that we could choose a better name for our custom method, for example `send_mail`. This is what explains the type of error we get, which isn't a `NoMethoError`, but an argument error. 



### Exercise 10: Does it Rock or Not?

https://launchschool.com/exercises/d1b5ac33