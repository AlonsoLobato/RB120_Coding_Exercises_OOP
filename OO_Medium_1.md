## RB120 - Object Oriented Programming --> OO Medium 1

### Exercise 1: Privacy

Consider the following class:

```ruby
class Machine
  attr_writer :switch

  def start
    self.flip_switch(:on)
  end

  def stop
    self.flip_switch(:off)
  end

  def flip_switch(desired_state)
    self.switch = desired_state
  end
end
```

Modify this class so both `flip_switch` and the setter method `switch=` are private methods.

#### Solution

```ruby
class Machine

  def start
    flip_switch(:on)
  end

  def stop
    flip_switch(:off)
  end

  private
  
  attr_writer :switch
  
  def flip_switch(desired_state)
    self.switch = desired_state
  end
end
```

We wanted to make private the `flip_switch` method and the `switch=` setter method, so they can only be accessed from inside the class. 

To do so we move both method to the bottom, under the `private` method call. Now both methods can only be accessed through `start` and `stop` methods. Note that, now, the use of explicit caller `self` before calling the private method `flip_switch` is not neccesary anymore (as of Ruby 2.7 this is optional, before this version it was prohibited).

#### Further Exploration

Add a private getter for `@switch` to the `Machine` class, and add a method to `Machine` that shows how to use that getter.

```ruby
class Machine

  def start
    flip_switch(:on)
  end

  def stop
    flip_switch(:off)
  end

  def print_state 
    case switch
      when :on then puts "The state is on"
      when :off then puts "The state is off"
      else puts "State unknown"
    end
  end
  
  private
  
  attr_accessor :switch
  
  def flip_switch(desired_state)
    self.switch = desired_state
  end
end

ab = Machine.new
ab.print_state      # State unknown
ab.start
ab.print_state      # The state is on
ab.stop
ab.print_state      # The state is off
```



### Exercise 2: Fixed Array

A fixed-length array is an array that always has a fixed number of elements. Write a class that implements a fixed-length array, and provides the necessary methods to support the following code. The code should output `true` 16 times.

```ruby
fixed_array = FixedArray.new(5)
puts fixed_array[3] == nil
puts fixed_array.to_a == [nil] * 5

fixed_array[3] = 'a'
puts fixed_array[3] == 'a'
puts fixed_array.to_a == [nil, nil, nil, 'a', nil]

fixed_array[1] = 'b'
puts fixed_array[1] == 'b'
puts fixed_array.to_a == [nil, 'b', nil, 'a', nil]

fixed_array[1] = 'c'
puts fixed_array[1] == 'c'
puts fixed_array.to_a == [nil, 'c', nil, 'a', nil]

fixed_array[4] = 'd'
puts fixed_array[4] == 'd'
puts fixed_array.to_a == [nil, 'c', nil, 'a', 'd']
puts fixed_array.to_s == '[nil, "c", nil, "a", "d"]'

puts fixed_array[-1] == 'd'
puts fixed_array[-4] == 'c'

begin
  fixed_array[6]
  puts false
rescue IndexError
  puts true
end

begin
  fixed_array[-7] = 3
  puts false
rescue IndexError
  puts true
end

begin
  fixed_array[7] = 3
  puts false
rescue IndexError
  puts true
end
```

#### Solution

```ruby
class FixedArray
  
  attr_accessor :array
  
  def initialize(length)
    @array = [nil] * length
  end
  
  def [](idx)
    if idx >= array.length || -idx >= array.length
      raise IndexError
    else
      array[idx]
    end
  end
  
  def []=(idx, item)
    if idx >= array.length || -idx >= array.length
      raise IndexError
    else
      array[idx] = item   
    end
  end
  
  def to_a
    array.to_a
  end
  
  def to_s
    array.to_s
  end
end

fixed_array = FixedArray.new(5)

puts fixed_array[3] == nil
puts fixed_array.to_a == [nil] * 5

fixed_array[3] = 'a'
puts fixed_array[3] == 'a'
puts fixed_array.to_a == [nil, nil, nil, 'a', nil]

fixed_array[1] = 'b'
puts fixed_array[1] == 'b'
puts fixed_array.to_a == [nil, 'b', nil, 'a', nil]

fixed_array[1] = 'c'
puts fixed_array[1] == 'c'
puts fixed_array.to_a == [nil, 'c', nil, 'a', nil]

fixed_array[4] = 'd'
puts fixed_array[4] == 'd'
puts fixed_array.to_a == [nil, 'c', nil, 'a', 'd']
puts fixed_array.to_s == '[nil, "c", nil, "a", "d"]'

puts fixed_array[-1] == 'd'
puts fixed_array[-4] == 'c'

begin
  fixed_array[6]
  puts false
rescue IndexError
  puts true
end

begin
  fixed_array[-7] = 3
  puts false
rescue IndexError
  puts true
end

begin
  fixed_array[7] = 3
  puts false
rescue IndexError
  puts true
end
```

I opted for creating a new class `FixedArray` that by defaults inherits from the `Array` class, overriding some of its methods to rise `IndexError` error when required according to the given examples. However it is generally considered bad practice to inherit from standard classes; instead, we should use a collaborator object.

#### Proposed solution

```ruby
class FixedArray
  def initialize(max_size)
    @array = Array.new(max_size)    # (1)
  end

  def [](index)
    @array.fetch(index)             # (2)
  end

  def []=(index, value)
    self[index]                     # (3) 
    @array[index] = value
  end

  def to_a
    @array.clone                    # (4)
  end

  def to_s       
    to_a.to_s                       # (5) 
  end
end
```

1. Initializing the `@array` ivar to instances of the `Array` class
2. The `fetch` method rises an `IndexError` when an index that hasn't been created is accessed (as oppose as the normal `[]` element reference which returns `nil` if a "non existing" index is referenced).
3. Raise error if index is invalid; here we are using the getter method defined in the previous step; if the getter rises the error, next line, the setter, isn't executed.
4.  We use `clone` to get a new array, so any change that we can make to it won't affect the original array of `Array` objects
5. Finally, to transform our array to string, we use `to_s` on the return value of the `to_a` that we defined right above. 



### Exercise 3: Students

Below we have 3 classes: `Student`, `Graduate`, and `Undergraduate`. The implementation details for the `#initialize` methods in `Graduate` and `Undergraduate` are missing. Fill in those missing details so that the following requirements are fulfilled:

1. Graduate students have the option to use on-campus parking, while Undergraduate students do not.
2. Graduate and Undergraduate students both have a name and year associated with them.

```ruby
class Student
  def initialize(name, year)
    @name = name
    @year = year
  end
end

class Graduate
  def initialize(name, year, parking)
  end
end

class Undergraduate
  def initialize(name, year)
  end
end
```

### Solution

```ruby
class Student
  def initialize(name, year)
    @name = name
    @year = year
  end
end

class Graduate < Student
  def initialize(name, year, parking = true)
    super(name, year)
    @parking = parking
  end
end

class Undergraduate < Student          
  def initialize(name, year)             # (1)
    super
  end
end

alonso = Graduate.new('Alonso', 2005)
katie = Undergraduate.new('Katie', 2015)
p alonso
p katie
```

1. The `initialize` included in the `Undergraduate` class can be removed completely as it does not add anything to the `initialize` defined in the superclass from which `Undergraduate` inherits from.

#### Further exploration

There is one other "form" of the keyword `super`. We can call it as `super()`. This calls the superclass method of the same name as the calling method, but here no arguments are passed to the superclass method at all. Can you think of a way to use `super()` in another Student related class?

```ruby
class StudentBody
  @@total_students = 0

  def initialize
    @@total_students += 1
    @student_number = @@total_students
  end
  
  def self.display_total_students
    @@total_students
  end
  
  def display_student_number
    @student_number
  end
end

class Student < StudentBody
  def initialize(name, year)
    @name = name
    @year = year
    super()           # (1)
  end
end

class Graduate < Student
  def initialize(name, year, parking)
    super(name, year)
    @parking = parking
  end
end

class Undergraduate < Student
  def initialize(name, year)
    super
  end
end

alonso = Graduate.new('Alonso', 1999, true)
katie = Undergraduate.new('Katie', 2007)
p alonso
p katie
p alonso.display_student_number
# 1
p katie.display_student_number
# 2
p Student.display_total_students
# 2
```

1. Here is where `super()` is invoked, with no arguments passed to the parent's initialize method. So the student counter adds plus one every time a student of any sort is instantiated.



### Exercise 4: Circular queue

A [circular queue](https://en.wikipedia.org/wiki/Circular_buffer) is a collection of objects stored in a buffer that is treated as though it is connected end-to-end in a circle. When an object is added to this circular queue, it is added to the position that immediately follows the most recently added object, while removing an object always removes the object that has been in the queue the longest.

This works as long as there are empty spots in the buffer. If the buffer becomes full, adding a new object to the queue requires getting rid of an existing object; with a circular queue, the object that has been in the queue the longest is discarded and replaced by the new object.

Your task is to write a `CircularQueue` class that implements a circular queue for arbitrary objects. The class should obtain the buffer size with an argument provided to `CircularQueue::new`, and should provide the following methods:

- `enqueue` to add an object to the queue
- `dequeue` to remove (and return) the oldest object in the queue. It should return `nil` if the queue is empty.

You may assume that none of the values stored in the queue are `nil` (however, `nil` may be used to designate empty spots in the buffer).

```ruby
# All the following test cases should return true

queue = CircularQueue.new(3)
puts queue.dequeue == nil

queue.enqueue(1)
queue.enqueue(2)
puts queue.dequeue == 1

queue.enqueue(3)
queue.enqueue(4)
puts queue.dequeue == 2

queue.enqueue(5)
queue.enqueue(6)
queue.enqueue(7)
puts queue.dequeue == 5
puts queue.dequeue == 6
puts queue.dequeue == 7
puts queue.dequeue == nil

queue = CircularQueue.new(4)
puts queue.dequeue == nil

queue.enqueue(1)
queue.enqueue(2)
puts queue.dequeue == 1

queue.enqueue(3)
queue.enqueue(4)
puts queue.dequeue == 2

queue.enqueue(5)
queue.enqueue(6)
queue.enqueue(7)
puts queue.dequeue == 4
puts queue.dequeue == 5
puts queue.dequeue == 6
puts queue.dequeue == 7
puts queue.dequeue == nil
```

#### Solution

```ruby
class CircularQueue
  
  attr_reader :max_size, :buffer          # (1)
  
  def initialize(max_size)                # (2)
    @max_size = size
    @buffer = []
  end
  
  def enqueue(obj)                        # (3)
    if buffer.size == size
      dequeue
      buffer << obj
    else
      buffer << obj
    end
  end
  
  def dequeue                             # (4)
    buffer.shift
  end
end
```

1. First we create getter methods for the `max_size` and `buffer` ivars
2. The `initialize` method takes one argument, the maximum size of the buffer, assigned to `@max_size`. We also initialize a `@buffer` ivar set to an empty array.
3. The instance method `enqueue` is defined first. This method appends a new object (passed in as an argument to the method when invoked) to the buffer when invoked. If the buffer is full (current size of buffer is equal to max size allowed), then we need to remove the oldest element from the buffer. We do that by calling the `dequeue` instance method.
4. The `dequeue` instance method removes elements from the `@buffer` array using `shift`. This method *removes the first element of `self` and returns it (shifting all other elements down by one). Returns `nil` if the array is empty*. 
5. This simple code provides us with the wanted solution: new elements are added to the end, deleted elements are deleted from the start of the array.