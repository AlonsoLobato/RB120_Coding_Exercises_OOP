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
    # other code ommited
    
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

  