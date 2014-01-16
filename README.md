# The Ruby Style Guide

Our style guide is a fork of
[bbatsov's great work](https://github.com/bbatsov/ruby-style-guide).
We mainly stripped it a bit down and punctually changed rules.

You can generate a PDF or an HTML copy of this guide using
[Transmuter](https://github.com/TechnoGate/transmuter).

[RuboCop](https://github.com/bbatsov/rubocop) is a code analyzer,
based on this style guide.

## Table of Contents

* [Source Code Layout](#source-code-layout)
* [Syntax](#syntax)
* [Naming](#naming)
* [Comments](#comments)
    * [Comment Annotations](#comment-annotations)
* [Classes](#classes--modules)
* [Exceptions](#exceptions)
* [Collections](#collections)
* [Strings](#strings)
* [Regular Expressions](#regular-expressions)
* [Percent Literals](#percent-literals)
* [Metaprogramming](#metaprogramming)
* [Misc](#misc)
* [Tools](#tools)

## Source Code Layout

> Nearly everybody is convinced that every style but their own is
> ugly and unreadable. Leave out the "but their own" and they're
> probably right... <br/>
> -- Jerry Coffin (on indentation)

* Use `UTF-8` as the source file encoding.
* Use two **spaces** per indentation level. No hard tabs.

    ```Ruby
    # bad - four spaces
    def some_method
        do_something
    end

    # good
    def some_method
      do_something
    end
    ```

* Use Unix-style line endings. (*BSD/Solaris/Linux/OSX users are covered by default,
  Windows users have to be extra careful.)
    * If you're using Git you might want to add the following
    configuration setting to protect your project from Windows line
    endings creeping in:

    ```bash
    $ git config --global core.autocrlf true
    ```

* End each file with a blank newline
* Don't use `;` to separate statements and expressions. As a
  corollary - use one expression per line.

    ```Ruby
    # bad
    puts 'foobar'; # superfluous semicolon

    puts 'foo'; puts 'bar' # two expression on the same line

    # good
    puts 'foobar'

    puts 'foo'
    puts 'bar'

    puts 'foo', 'bar' # this applies to puts in particular
    ```

* Prefer a single-line format for class definitions with no body.

    ```Ruby
    # bad
    class FooError < StandardError
    end

    # bad
    class FooError < StandardError; end

    # good
    FooError = Class.new(StandardError)
    ```

* Avoid single-line methods. Although they are somewhat popular in the
  wild, there are a few peculiarities about their definition syntax
  that make their use undesirable. At any rate - there should be no more
  than one expression in a single-line method.

    ```Ruby
    # bad
    def too_much; something; something_else; end

    # bad (notice that the first ; is required)
    def no_braces_method; body end

    # bad (notice that the second ; is optional)
    def no_braces_method; body; end

    # bad (valid syntax, but no ; make it kind of hard to read)
    def some_method() body end

    # good
    def some_method
      body
    end
    ```

    No exception to the rule are empty-body methods.

    ```Ruby
    # good
    def no_op
    end
    ```

* Use spaces around operators, after commas, colons and semicolons, around `{`
  and before `}`. Whitespace might be (mostly) irrelevant to the Ruby
  interpreter, but its proper use is the key to writing easily
  readable code.

    ```Ruby
    sum = 1 + 2
    a, b = 1, 2
    1 > 2 ? true : false; puts 'Hi'
    [1, 2, 3].each { |e| puts e }
    ```

    The only exception, regarding operators, is the exponent operator:

    ```Ruby
    # bad
    e = M * c ** 2

    # good
    e = M * c**2
    ```

    `{` and `}` deserve a bit of clarification, since they are used
    for block and hash literals, as well as embedded expressions in
    strings. For hash literals only the second of the following styles is
    considered acceptable.

    ```Ruby
    # bad - space after { and before }
    { one: 1, two: 2 }

    # good - no space after { and before }
    {one: 1, two: 2}
    ```

    As far as embedded expressions go, there is also only one acceptable
    option:

    ```Ruby
    # good - no spaces
    "string#{expr}"

    # bad
    "string#{ expr }"
    ```

    The first style is extremely more popular and you're generally
    advised to stick with it. The second, on the other hand, is
    (arguably) a bit more readable. As with hashes - pick one style
    and apply it consistently.

* No spaces after `(`, `[` or before `]`, `)`.

    ```Ruby
    some(arg).other
    [1, 2, 3].length
    ```

* No space after `!`.

    ```Ruby
    # bad
    ! something

    # good
    !something
    ```

* Indent `when` as deep as `case`. I know that many would disagree
  with this one, but it's the style established in both "The Ruby
  Programming Language" and "Programming Ruby".

    ```Ruby
    # bad
    case
      when song.name == 'Misty'
        puts 'Not again!'
      when song.duration > 120
        puts 'Too long!'
      when Time.now.hour > 21
        puts "It's too late"
      else
        song.play
    end

    # good
    case
    when song.name == 'Misty'
      puts 'Not again!'
    when song.duration > 120
      puts 'Too long!'
    when Time.now.hour > 21
      puts "It's too late"
    else
      song.play
    end
    ```

* When assigning the result of a conditional expression to a variable, preserve the usual alignment of its branches.

    ```Ruby
    # bad - pretty convoluted
    kind = case year
    when 1850..1889 then 'Blues'
    when 1890..1909 then 'Ragtime'
    when 1910..1929 then 'New Orleans Jazz'
    when 1930..1939 then 'Swing'
    when 1940..1950 then 'Bebop'
    else 'Jazz'
    end

    result = if some_cond
      calc_something
    else
      calc_something_else
    end

    # bad - it's apparent what's going on
    kind = case year
           when 1850..1889 then 'Blues'
           when 1890..1909 then 'Ragtime'
           when 1910..1929 then 'New Orleans Jazz'
           when 1930..1939 then 'Swing'
           when 1940..1950 then 'Bebop'
           else 'Jazz'
           end

    result = if some_cond
               calc_something
             else
               calc_something_else
             end

    # good (and a bit more width efficient)
    kind =
      case year
      when 1850..1889 then 'Blues'
      when 1890..1909 then 'Ragtime'
      when 1910..1929 then 'New Orleans Jazz'
      when 1930..1939 then 'Swing'
      when 1940..1950 then 'Bebop'
      else 'Jazz'
      end

    result =
      if some_cond
        calc_something
      else
        calc_something_else
      end
    ```

* Use empty lines between method definitions and also to break up a method into logical
  paragraphs internally.

    ```Ruby
    def some_method
      data = initialize(options)

      data.manipulate!

      data.result
    end

    def some_method
      result
    end
    ```

* Use spaces around the `=` operator when assigning default values to method parameters:

    ```Ruby
    # bad
    def some_method(arg1=:default, arg2=nil, arg3=[])
      # do something...
    end

    # good
    def some_method(arg1 = :default, arg2 = nil, arg3 = [])
      # do something...
    end
    ```

    While several Ruby books suggest the first style, the second is much more prominent
    in practice (and arguably a bit more readable).

* Avoid line continuation `\` where not required. In practice, avoid using
  line continuations for anything but string concatenation.

    ```Ruby
    # ok
    result = 1 - \
             2

    # good (but still ugly as hell)
    result = 1 \
             - 2

    long_string = 'First part of the long string' \
                  ' and second part of the long string'
    ```

* When continuing a chained method invocation on another line keep the `.` on the second line.

    ```Ruby
    # bad - need to consult first line to understand second line
    one.two.three.
      four

    # good - it's immediately clear what's going on the second line
    one.two.three
      .four
    ```

* Align the parameters of a method call if they span more than one
  line. When aligning parameters is not appropriate due to line-length
  constraints, single indent for the lines after the first is also
  acceptable.

    ```Ruby
    # bad: starting point (line is too long)
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com', from: 'us@example.com', subject: 'Important message', body: source.text)
    end

    # bad (double indent)
    def send_mail(source)
      Mailer.deliver(
          to: 'bob@example.com',
          from: 'us@example.com',
          subject: 'Important message',
          body: source.text)
    end

    # good
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com',
                     from: 'us@example.com',
                     subject: 'Important message',
                     body: source.text)
    end

    # good (normal indent)
    def send_mail(source)
      Mailer.deliver(
        to: 'bob@example.com',
        from: 'us@example.com',
        subject: 'Important message',
        body: source.text
      )
    end
    ```

* Align the elements of array literals spanning multiple lines.

    ```Ruby
    # bad - single indent
    menu_item = ["Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam",
      "Baked beans", "Spam", "Spam", "Spam", "Spam", "Spam"]

    # good
    menu_item = [
      "Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam",
      "Baked beans", "Spam", "Spam", "Spam", "Spam", "Spam"
    ]

    # good
    menu_item =
      ["Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam",
       "Baked beans", "Spam", "Spam", "Spam", "Spam", "Spam"]
    ```

* Add underscores to large numeric literals to improve their readability.

    ```Ruby
    # bad - how many 0s are there?
    num = 1000000

    # good - much easier to parse for the human brain
    num = 1_000_000
    ```

* Limit lines to 80 characters.
* Avoid trailing whitespace.
* Don't use block comments. They cannot be preceded by whitespace and are not
as easy to spot as regular comments.

    ```Ruby
    # bad
    == begin
    comment line
    another comment line
    == end

    # good
    # comment line
    # another comment line
    ```

## Syntax

* Use `::` only to reference constants(this includes classes and
modules) and constructors (like `Array()` or `Nokogiri::HTML()`).
Never use `::` for regular method invocation.

    ```Ruby
    # bad
    SomeClass::some_method
    some_object::some_method

    # good
    SomeClass.some_method
    some_object.some_method
    SomeModule::SomeClass::SOME_CONST
    SomeModule::SomeClass()
    ```

* Never use `for`, unless you know exactly why. Most of the time iterators
  should be used instead. `for` is implemented in terms of `each` (so
  you're adding a level of indirection), but with a twist - `for`
  doesn't introduce a new scope (unlike `each`) and variables defined
  in its block will be visible outside it.

    ```Ruby
    arr = [1, 2, 3]

    # bad
    for elem in arr do
      puts elem
    end

    # note that elem is accessible outside of the for loop
    elem #=> 3

    # good
    arr.each { |elem| puts elem }

    # elem is not accessible outside each's block
    elem #=> NameError: undefined local variable or method `elem'
    ```

* Never use `then` for multi-line `if/unless`.

    ```Ruby
    # bad
    if some_condition then
      # body omitted
    end

    # good
    if some_condition
      # body omitted
    end
    ```

* Always put the condition on the same line as the `if`/`unless` in a multi-line conditional.

    ```Ruby
    # bad
    if
      some_condition
      do_something
      do_something_else
    end

    # good
    if some_condition
      do_something
      do_something_else
    end
    ```

* Favor the ternary operator(`?:`) over `if/then/else/end` constructs.
  It's more common and obviously more concise.

    ```Ruby
    # bad
    result = if some_condition then something else something_else end

    # good
    result = some_condition ? something : something_else
    ```

* Use one expression per branch in a ternary operator. This
  also means that ternary operators must not be nested. Prefer
  `if/else` constructs in these cases.

    ```Ruby
    # bad
    some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

    # good
    if some_condition
      nested_condition ? nested_something : nested_something_else
    else
      something_else
    end
    ```

* Never use `if x: ...` - as of Ruby 1.9 it has been removed. Use
  the ternary operator instead.

    ```Ruby
    # bad
    result = if some_condition: something else something_else end

    # good
    result = some_condition ? something : something_else
    ```

* Never use `if x; ...`. Use the ternary operator instead.

* Use `when x then ...` for one-line cases. The alternative syntax
  `when x: ...` has been removed as of Ruby 1.9.

* Never use `when x; ...`. See the previous rule.

* Use `!` instead of `not`.

    ```Ruby
    # bad - braces are required because of op precedence
    x = (not something)

    # good
    x = !something
    ```

* Avoid the use of `!!`.

    ```Ruby
    # bad
    x = 'test'
    # obscure nil check
    if !!x
      # body omitted
    end

    x = false
    # double negation is useless on booleans
    !!x # => false

    # good
    x = 'test'
    if !x.nil?
      # body omitted
    end
    ```

* The `and` and `or` keywords are banned. It's just not worth
  it. Always use `&&` and `||` instead.

    ```Ruby
    # bad
    # boolean expression
    if some_condition and some_other_condition
      do_something
    end

    # control flow
    document.saved? or document.save!

    # good
    # boolean expression
    if some_condition && some_other_condition
      do_something
    end

    # control flow
    document.saved? || document.save!
    ```

* Favor modifier `if/unless` usage when you have a single-line
  body. Another good alternative is the usage of control flow `&&/||`.

    ```Ruby
    # bad
    if some_condition
      do_something
    end

    # good
    do_something if some_condition

    # another good option
    some_condition && do_something
    ```

* Favor `unless` over `if` for negative conditions (or control
  flow `||`).

    ```Ruby
    # bad
    do_something if !some_condition

    # bad
    do_something if not some_condition

    # bad
    some_condition || do_something

    # good
    do_something unless some_condition
    ```

* Never use `unless` with `else`. Rewrite these with the positive case first.

    ```Ruby
    # bad
    unless success?
      puts 'failure'
    else
      puts 'success'
    end

    # good
    if success?
      puts 'success'
    else
      puts 'failure'
    end
    ```

* Don't use parentheses around the condition of an `if/unless/while/until`.

    ```Ruby
    # bad
    if (x > 10)
      # body omitted
    end

    # good
    if x > 10
      # body omitted
    end
    ```

* Never use `while/until condition do` for multi-line `while/until`.

    ```Ruby
    # bad
    while x > 5 do
      # body omitted
    end

    until x > 5 do
      # body omitted
    end

    # good
    while x > 5
      # body omitted
    end

    until x > 5
      # body omitted
    end
    ```

* Favor modifier `while/until` usage when you have a single-line
  body.

    ```Ruby
    # bad
    while some_condition
      do_something
    end

    # good
    do_something while some_condition
    ```

* Favor `until` over `while` for negative conditions.

    ```Ruby
    # bad
    do_something while !some_condition

    # good
    do_something until some_condition
    ```

* Use `Kernel#loop` with break rather than `begin/end/until` or `begin/end/while` for post-loop tests.

   ```Ruby
   # bad
   begin
     puts val
     val += 1
   end while val < 0

   # good
   loop do
     puts val
     val += 1
     break unless val < 0
   end
   ```

* Omit the outer braces around an implicit options hash.

    ```Ruby
    # bad
    user.set({ name: 'John', age: 45, permissions: { read: true } })

    # good
    User.set(name: 'John', age: 45, permissions: { read: true })
    ```

* Omit both the outer braces and parentheses for methods that are
  part of an internal DSL.

    ```Ruby
    class Person < ActiveRecord::Base
      # bad
      validates(:name, { presence: true, length: { within: 1..10 } })

      # good
      validates :name, presence: true, length: { within: 1..10 }
    end
    ```

* Omit parentheses for method calls with no arguments.

    ```Ruby
    # bad
    Kernel.exit!()
    2.even?()
    fork()
    'test'.upcase()

    # good
    Kernel.exit!
    2.even?
    fork
    'test'.upcase
    ```

* Prefer `{...}` over `do...end` for single-line blocks.  Avoid using
  `{...}` for multi-line blocks (multiline chaining is always
  ugly). Always use `do...end` for "control flow" and "method
  definitions" (e.g. in Rakefiles and certain DSLs).

    ```Ruby
    names = ['Bozhidar', 'Steve', 'Sarah']

    # bad
    names.each do |name|
      puts name
    end

    # good
    names.each { |name| puts name }

    # good
    names.select do |name|
      name.start_with?('S')
    end.map { |name| name.upcase }

    # good
    names.select { |name| name.start_with?('S') }.map { |name| name.upcase }
    ```

    Some will argue that multiline chaining would look OK with the use of {...}, but they should
    ask themselves - is this code really readable and can the blocks' contents be extracted into
    nifty methods?

* Avoid `return` where not required for flow of control.

    ```Ruby
    # bad
    def some_method(some_arr)
      return some_arr.size
    end

    # good
    def some_method(some_arr)
      some_arr.size
    end
    ```

* Avoid `self` where not required. (It is only required when calling a self write accessor.)

    ```Ruby
    # bad
    def ready?
      if self.last_reviewed_at > self.last_updated_at
        self.worker.update(self.content, self.options)
        self.status = :in_progress
      end
      self.status == :verified
    end

    # good
    def ready?
      if last_reviewed_at > last_updated_at
        worker.update(content, options)
        self.status = :in_progress
      end
      status == :verified
    end
    ```

* As a corollary, avoid shadowing methods with local variables unless they are both equivalent.

    ```Ruby
    class Foo
      attr_accessor :options

      # ok
      def initialize(options)
        self.options = options
        # both options and self.options are equivalent here
      end

      # bad
      def do_something(options = {})
        unless options[:when] == :later
          output(self.options[:message])
        end
      end

      # good
      def do_something(params = {})
        unless params[:when] == :later
          output(options[:message])
        end
      end
    end
    ```

* Use `||=` freely to initialize variables.

    ```Ruby
    # set name to Bozhidar, only if it's nil or false
    name ||= 'Bozhidar'
    ```

* Don't use `||=` to initialize boolean variables. (Consider what
would happen if the current value happened to be `false`.)

    ```Ruby
    # bad - would set enabled to true even if it was false
    enabled ||= true

    # good
    enabled = true if enabled.nil?
    ```

* Avoid using Perl-style special variables (like `$:`, `$;`,
  etc. ). They are quite cryptic and their use in anything but
  one-liner scripts is discouraged. Use the human-friendly
  aliases provided by the `English` library.

    ```Ruby
    # bad
    $:.unshift File.dirname(__FILE__)

    # good
    require 'English'
    $LOAD_PATH.unshift File.dirname(__FILE__)
    ```

* Never put a space between a method name and the opening parenthesis.

    ```Ruby
    # bad
    f (3 + 2) + 1

    # good
    f(3 + 2) + 1
    ```

* If the first argument to a method begins with an open parenthesis,
  always use parentheses in the method invocation. For example, write
`f((3 + 2) + 1)`.

* Use the new lambda literal syntax for single line body blocks. Use the
  `lambda` method for multi-line blocks.

    ```Ruby
    # bad
    l = lambda { |a, b| a + b }
    l.call(1, 2)

    # correct, but looks extremely awkward
    l = ->(a, b) do
      tmp = a * 7
      tmp * b / 50
    end

    # good
    l = ->(a, b) { a + b }
    l.call(1, 2)

    l = lambda do |a, b|
      tmp = a * 7
      tmp * b / 50
    end
    ```

* Prefer `proc` over `Proc.new`.

    ```Ruby
    # bad
    p = Proc.new { |n| puts n }

    # good
    p = proc { |n| puts n }
    ```

* Prefer `proc.call()` over `proc[]` or `proc.()` for both lambdas and procs.

    ```Ruby
    # bad - looks similar to Enumeration access
    l = ->(v) { puts v }
    l[1]

    # also bad - uncommon syntax
    l = ->(v) { puts v }
    l.(1)

    # good
    l = ->(v) { puts v }
    l.call(1)
    ```

* Use `_` for unused block parameters.

    ```Ruby
    # bad
    result = hash.map { |k, v| v + 1 }

    # good
    result = hash.map { |_, v| v + 1 }
    ```

* Use `$stdout/$stderr/$stdin` instead of
  `STDOUT/STDERR/STDIN`. `STDOUT/STDERR/STDIN` are constants, and
  while you can actually reassign (possibly to redirect some stream)
  constants in Ruby, you'll get an interpreter warning if you do so.

* Use `warn` instead of `$stderr.puts`. Apart from being more concise
and clear, `warn` allows you to suppress warnings if you need to (by
setting the warn level to 0 via `-W0`).

* Favor the use of `sprintf` and its alias `format` over the fairly
  cryptic `String#%` method.

    ```Ruby
    # bad
    '%d %d' % [20, 10]
    # => '20 10'

    # good
    sprintf('%d %d', 20, 10)
    # => '20 10'

    # good
    sprintf('%{first} %{second}', first: 20, second: 10)
    # => '20 10'

    format('%d %d', 20, 10)
    # => '20 10'

    # good
    format('%{first} %{second}', first: 20, second: 10)
    # => '20 10'
    ```

* Favor the use of `Array#join` over the fairly cryptic `Array#*` with
  a string argument.

    ```Ruby
    # bad
    %w(one two three) * ', '
    # => 'one, two, three'

    # good
    %w(one two three).join(', ')
    # => 'one, two, three'
    ```

* Use `[*var]` or `Array()` instead of explicit `Array` check, when dealing with a
  variable you want to treat as an Array, but you're not certain it's
  an array.

    ```Ruby
    # bad
    paths = [paths] unless paths.is_a? Array
    paths.each { |path| do_something(path) }

    # good
    [*paths].each { |path| do_something(path) }

    # good (and a bit more readable)
    Array(paths).each { |path| do_something(path) }
    ```

* Use ranges or `Comparable#between?` instead of complex comparison logic when possible.

    ```Ruby
    # bad
    do_something if x >= 1000 && x <= 2000

    # good
    do_something if (1000..2000).include?(x)

    # good
    do_something if x.between?(1000, 2000)
    ```

* Favor the use of predicate methods to explicit comparisons with
  `==`. Numeric comparisons are OK.

    ```Ruby
    # bad
    if x % 2 == 0
    end

    if x % 2 == 1
    end

    if x == nil
    end

    # good
    if x.even?
    end

    if x.odd?
    end

    if x.nil?
    end

    if x.zero?
    end

    if x == 0
    end
    ```

* Avoid the use of `BEGIN` blocks.

* Never use `END` blocks. Use `Kernel#at_exit` instead.

    ```ruby
    # bad

    END { puts 'Goodbye!' }

    # good

    at_exit { puts 'Goodbye!' }
    ```

* Avoid the use of flip-flops.

* Avoid use of nested conditionals for flow of control.
  Prefer a guard clause when you can assert invalid data. A guard clause is a conditional
  statement at the top of a function that bails out as soon as it can.

    ```Ruby
    # bad
      def compute_thing(thing)
        if thing[:foo]
          update_with_bar(thing)
          if thing[:foo][:bar]
            partial_compute(thing)
          else
            re_compute(thing)
          end
        end
      end

    # good
      def compute_thing(thing)
        return unless thing[:foo]
        update_with_bar(thing[:foo])
        return re_compute(thing) unless thing[:foo][:bar]
        partial_compute(thing)
      end
    ```

## Naming

> The only real difficulties in programming are cache invalidation and
> naming things. <br/>
> -- Phil Karlton

* Name identifiers in English.

    ```Ruby
    # bad - identifier using non-ascii characters
    заплата = 1_000

    # bad - identifier is a Bulgarian word, written with Latin letters (instead of Cyrillic)
    zaplata = 1_000

    # good
    salary = 1_000
    ```

* Use `snake_case` for symbols, methods and variables.

    ```Ruby
    # bad
    :'some symbol'
    :SomeSymbol
    :someSymbol

    someVar = 5

    def someMethod
      ...
    end

    def SomeMethod
     ...
    end

    # good
    :some_symbol

    def some_method
      ...
    end
    ```

* Use `CamelCase` for classes and modules.

    ```Ruby
    # bad
    class Someclass
      ...
    end

    class Some_Class
      ...
    end

    class SomeXml
      ...
    end

    # good
    class SomeClass
      ...
    end

    class SomeXML
      ...
    end
    ```

* Use `SCREAMING_SNAKE_CASE` for other constants.

    ```Ruby
    # bad
    SomeConst = 5

    # good
    SOME_CONST = 5
    ```

* The names of predicate methods (methods that return a boolean value)
  should end in a question mark.
  (i.e. `Array#empty?`).
* The names of potentially *dangerous* methods (i.e. methods that
  modify `self` or the arguments, `exit!` (doesn't run the finalizers
  like `exit` does), etc.) should end with an exclamation mark if
  there exists a safe version of that *dangerous* method.

    ```Ruby
    # bad - there is not matching 'safe' method
    class Person
      def update!
      end
    end

    # good
    class Person
      def update
      end
    end

    # good
    class Person
      def update!
      end

      def update
      end
    end
    ```

* Define the non-bang (safe) method in terms of the bang (dangerous)
  one if possible.

    ```Ruby
    class Array
      def flatten_once!
        res = []

        each do |e|
          [*e].each { |f| res << f }
        end

        replace(res)
      end

      def flatten_once
        dup.flatten_once!
      end
    end
    ```

* Prefer `map` over `collect`, `find` over `detect`, `select` over
  `find_all`, `reduce` over `inject` and `size` over `length`. This is
  not a hard requirement; if the use of the alias enhances
  readability, it's ok to use it. The rhyming methods are inherited from
  Smalltalk and are not common in other programming languages. The
  reason the use of `select` is encouraged over `find_all` is that it
  goes together nicely with `reject` and its name is pretty self-explanatory.

* Use `flat_map` instead of `map` + `flatten`.
  This does not apply for arrays with a depth greater than 2, i.e.
  if `users.first.songs == ['a', ['b','c']]`, then use `map + flatten` rather than `flat_map`.
  `flat_map` flattens the array by 1, whereas `flatten` flattens it all the way.

    ```Ruby
    # bad
    all_songs = users.map(&:songs).flatten.uniq

    # good
    all_songs = users.flat_map(&:songs).uniq
    ```

* Use `reverse_each` instead of `reverse.each`. `reverse_each` doesn't
  do a new array allocation and that's a good thing.


    ```Ruby
    # bad
    array.reverse.each { ... }

    # good
    array.reverse_each { ... }
    ```

## Comments

> Good code is its own best documentation. As you're about to add a
> comment, ask yourself, "How can I improve the code so that this
> comment isn't needed?" Improve the code and then document it to make
> it even clearer. <br/>
> -- Steve McConnell

* Write self-documenting code.
* Write comments in German.
* Use one space between the leading `#` character of the comment and the text
  of the comment.
* Comments longer than a word are capitalized and use punctuation. Use [one
  space](http://en.wikipedia.org/wiki/Sentence_spacing) after periods.
* Avoid superfluous comments.

    ```Ruby
    # bad
    counter += 1 # Erhöht counter um eins.
    ```

* Keep existing comments up-to-date. An outdated comment is worse than no comment
at all.

> Good code is like a good joke - it needs no explanation. <br/>
> -- Russ Olsen

* Avoid writing comments to explain bad code. Refactor the code to
  make it self-explanatory. (Do or do not - there is no try. --Yoda)

### Comment Annotations

* Annotations should usually be written on the line immediately above
  the relevant code.
* The annotation keyword is followed by a colon and a space, then a note
  describing the problem.
* If multiple lines are required to describe the problem, subsequent
  lines should be indented like this:

    ```Ruby
    def bar
      # TODO: Seit Version 3.2.1 von BarBazUtil kracht es hier gewaltig.
      baz(:quux)
    end
    ```

* In cases where the problem is so obvious that any documentation would
  be redundant, annotations may be left at the end of the offending line
  with no note. This usage should be the exception and not the rule.

    ```Ruby
    def bar
      sleep 100 # TODO: Optimieren
    end
    ```

* Use `TODO` to note missing features or functionality that should be
  added at a later date.
* Use `HACK` to note code smells where questionable coding practices
  were used and should be refactored away.

## Classes & Modules

* Use a consistent structure in your class definitions.

    ```Ruby
    class Person
      # extend and include go first
      extend SomeModule
      include AnotherModule

      # constants are next
      SOME_CONSTANT = 20

      # afterwards we have attribute macros
      attr_reader :name

      # followed by other macros (if any)
      validates :name

      # public class methods are next in line
      def self.some_method
      end

      # followed by public instance methods
      def some_method
      end

      # protected and private methods are grouped near the end
      protected

      def some_protected_method
      end

      private

      def some_private_method
      end
    end
    ```

* Prefer modules to classes with only class methods. Classes should be
  used only when it makes sense to create instances out of them.

    ```Ruby
    # bad
    class SomeClass
      def self.some_method
        # body omitted
      end

      def self.some_other_method
      end
    end

    # good
    module SomeClass
      module_function

      def some_method
        # body omitted
      end

      def some_other_method
      end
    end
    ```

* Use the `attr` family of functions to define trivial accessors or
mutators.

    ```Ruby
    # bad
    class Person
      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      def first_name
        @first_name
      end

      def last_name
        @last_name
      end
    end

    # good
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end
    end
    ```

* Avoid the use of `attr`. Use `attr_reader` and `attr_accessor` instead.

    ```Ruby
    # bad - creates a single attribute accessor (deprecated in 1.9)
    attr :something, true
    attr :one, :two, :three # behaves as attr_reader

    # good
    attr_accessor :something
    attr_reader :one, :two, :three
    ```

* Consider using `Struct.new`, which defines the trivial accessors,
constructor and comparison operators for you.

    ```Ruby
    # good
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end
    end

    # better
    Person = Struct.new(:first_name, :last_name) do
    end
    ````

* Prefer [duck-typing](http://en.wikipedia.org/wiki/Duck_typing) over inheritance.

    ```Ruby
    # bad
    class Animal
      # abstract method
      def speak
      end
    end

    # extend superclass
    class Duck < Animal
      def speak
        puts 'Quack! Quack'
      end
    end

    # extend superclass
    class Dog < Animal
      def speak
        puts 'Bau! Bau!'
      end
    end

    # good
    class Duck
      def speak
        puts 'Quack! Quack'
      end
    end

    class Dog
      def speak
        puts 'Bau! Bau!'
      end
    end
    ```

* Avoid the usage of class (`@@`) variables due to their "nasty" behavior
in inheritance.

    ```Ruby
    class Parent
      @@class_var = 'parent'

      def self.print_class_var
        puts @@class_var
      end
    end

    class Child < Parent
      @@class_var = 'child'
    end

    Parent.print_class_var # => will print "child"
    ```

    As you can see all the classes in a class hierarchy actually share one
    class variable. Class instance variables should usually be preferred
    over class variables.

* Assign proper visibility levels to methods (`private`, `protected`)
in accordance with their intended usage. Don't go off leaving
everything `public` (which is the default). After all we're coding
in *Ruby* now, not in *Python*.
* Indent the `public`, `protected`, and `private` methods as much the
  method definitions they apply to. Leave one blank line above the
  visibility modifier
  and one blank line below in order to emphasize that it applies to all
  methods below it.

    ```Ruby
    class SomeClass
      def public_method
        # ...
      end

      private

      def private_method
        # ...
      end

      def another_private_method
        # ...
      end
    end
    ```

* Use `def self.method` to define singleton methods. This makes the code
  easier to refactor since the class name is not repeated.
  Avoid `class << self` except when necessary, e.g. single accessors and aliased
  attributes.

    ```Ruby
    class TestClass
      # bad
      def TestClass.some_method
        # body omitted
      end

      # good
      def self.some_other_method
        # body omitted
      end

      # Avoid the following style
      class << self
        def first_method
          # body omitted
        end

        def second_method_etc
          # body omitted
        end
      end

      # Necessary
      class << self
        attr_accessor :per_page
        alias_method :items_per_page, :per_page
      end
    end
    ```

## Exceptions

* Don't specify `RuntimeError` explicitly in the two argument version of `fail/raise`.

    ```Ruby
    # bad
    fail RuntimeError, 'message'

    # good - signals a RuntimeError by default
    fail 'message'
    ```

* Never return from an `ensure` block. If you explicitly return from a
  method inside an `ensure` block, the return will take precedence over
  any exception being raised, and the method will return as if no
  exception had been raised at all. In effect, the exception will be
  silently thrown away.

    ```Ruby
    def foo
      begin
        fail
      ensure
        return 'very bad idea'
      end
    end
    ```

* Use *implicit begin blocks* where possible.

    ```Ruby
    # bad
    def foo
      begin
        # main logic goes here
      rescue
        # failure handling goes here
      end
    end

    # good
    def foo
      # main logic goes here
    rescue
      # failure handling goes here
    end
    ```

* Mitigate the proliferation of `begin` blocks by using
  *contingency methods* (a term coined by Avdi Grimm).

    ```Ruby
    # bad
    begin
      something_that_might_fail
    rescue IOError
      # handle IOError
    end

    begin
      something_else_that_might_fail
    rescue IOError
      # handle IOError
    end

    # good
    def with_io_error_handling
       yield
    rescue IOError
      # handle IOError
    end

    with_io_error_handling { something_that_might_fail }

    with_io_error_handling { something_else_that_might_fail }
    ```

* Don't suppress exceptions.

    ```Ruby
    # bad
    begin
      # an exception occurs here
    rescue SomeError
      # the rescue clause does absolutely nothing
    end

    # bad
    do_something rescue nil
    ```

* Avoid using `rescue` in its modifier form.

    ```Ruby
    # bad - this catches exceptions of StandardError class and its descendant classes
    read_file rescue handle_error($!)

    # good - this catches only the exceptions of Errno::ENOENT class and its descendant classes
    def foo
      read_file
    rescue Errno::ENOENT => ex
      handle_error(ex)
    end
    ```


* Don't use exceptions for flow of control.

    ```Ruby
    # bad
    begin
      n / d
    rescue ZeroDivisionError
      puts 'Cannot divide by 0!'
    end

    # good
    if d.zero?
      puts 'Cannot divide by 0!'
    else
      n / d
    end
    ```

* Avoid rescuing the `Exception` class.  This will trap signals and calls to
  `exit`, requiring you to `kill -9` the process.

    ```Ruby
    # bad
    begin
      # calls to exit and kill signals will be caught (except kill -9)
      exit
    rescue Exception
      puts "you didn't really want to exit, right?"
      # exception handling
    end

    # good
    begin
      # a blind rescue rescues from StandardError, not Exception as many
      # programmers assume.
    rescue => e
      # exception handling
    end

    # also good
    begin
      # an exception occurs here

    rescue StandardError => e
      # exception handling
    end

    ```

* Put more specific exceptions higher up the rescue chain, otherwise
  they'll never be rescued from.

    ```Ruby
    # bad
    begin
      # some code
    rescue Exception => e
      # some handling
    rescue StandardError => e
      # some handling
    end

    # good
    begin
      # some code
    rescue StandardError => e
      # some handling
    rescue Exception => e
      # some handling
    end
    ```

* Release external resources obtained by your program in an ensure
block.

    ```Ruby
    f = File.open('testfile')
    begin
      # .. process
    rescue
      # .. handle error
    ensure
      f.close unless f.nil?
    end
    ```

* Favor the use of exceptions for the standard library over
introducing new exception classes.

## Collections

* Prefer literal array and hash creation notation (unless you need to
pass parameters to their constructors, that is).

    ```Ruby
    # bad
    arr = Array.new
    hash = Hash.new

    # good
    arr = []
    hash = {}
    ```

* Prefer `%w` to the literal array syntax when you need an array of
words(non-empty strings without spaces and special characters in them).
Apply this rule only to arrays with two or more elements.

    ```Ruby
    # bad
    STATES = ['draft', 'open', 'closed']

    # good
    STATES = %w(draft open closed)
    ```

* Prefer `%i` to the literal array syntax when you need an array of
symbols(and you don't need to maintain Ruby 1.9 compatibility). Apply
this rule only to arrays with two or more elements.

    ```Ruby
    # bad
    STATES = [:draft, :open, :closed]

    # good
    STATES = %i(draft open closed)
    ```

* Avoid the creation of huge gaps in arrays.

    ```Ruby
    arr = []
    arr[100] = 1 # now you have an array with lots of nils
    ```

* When accessing the first or last element from an array, prefer `first` or `last` over `[0]` or `[-1]`.

* Prefer symbols instead of strings as hash keys.

    ```Ruby
    # bad
    hash = { 'one' => 1, 'two' => 2, 'three' => 3 }

    # good
    hash = { one: 1, two: 2, three: 3 }
    ```

* Avoid the use of mutable objects as hash keys.
* Use the hash literal syntax when your hash keys are symbols.

    ```Ruby
    # bad
    hash = { :one => 1, :two => 2, :three => 3 }

    # good
    hash = { one: 1, two: 2, three: 3 }
    ```

* Use `Hash#key?` instead of `Hash#has_key?` and `Hash#value?` instead
  of `Hash#has_value?`. As noted
  [here](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/43765)
  by Matz, the longer forms are considered deprecated.

    ```Ruby
    # bad
    hash.has_key?(:test)
    hash.has_value?(value)

    # good
    hash.key?(:test)
    hash.value?(value)
    ```

* Introduce boolean default values for hash keys via `Hash#fetch` as opposed to
  using custom logic.

   ```Ruby
   batman = { name: 'Bruce Wayne', is_evil: false }

   # bad - if we just use || operator with falsy value we won't get the expected result
   batman[:is_evil] || true # => true

   # good - fetch work correctly with falsy values
   batman.fetch(:is_evil, true) # => false
   ```

* Rely on the fact that as of Ruby 1.9 hashes are ordered.
* Never modify a collection while traversing it.

## Strings

* Prefer string interpolation instead of string concatenation:

    ```Ruby
    # bad
    email_with_name = user.name + ' <' + user.email + '>'

    # good
    email_with_name = "#{user.name} <#{user.email}>"
    ```

* Prefer single-quoted strings when you don't need string interpolation or
  special symbols such as `\t`, `\n`, `'`, etc.

    ```Ruby
    # bad
    name = "Bozhidar"

    # good
    name = 'Bozhidar'
    ```

* Don't leave out `{}` around instance and global variables being
  interpolated into a string.

    ```Ruby
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      # bad - valid, but awkward
      def to_s
        "#@first_name #@last_name"
      end

      # good
      def to_s
        "#{@first_name} #{@last_name}"
      end
    end

    $global = 0
    # bad
    puts "$global = #$global"

    # good
    puts "$global = #{$global}"
    ```

* Avoid using `String#+` when you need to construct large data chunks.
  Instead, use `String#<<`. Concatenation mutates the string instance in-place
  and is always faster than `String#+`, which creates a bunch of new string objects.

    ```Ruby
    # good and also fast
    html = ''
    html << '<h1>Page title</h1>'

    paragraphs.each do |paragraph|
      html << "<p>#{paragraph}</p>"
    end
    ```

* When using heredocs for multi-line strings keep in mind the fact
  that they preserve leading whitespace. It's a good practice to
  employ some margin based on which to trim the excessive whitespace.

    ```Ruby
    code = <<-END.gsub(/^\s+\|/, '')
      |def test
      |  some_method
      |  other_method
      |end
    END
    #=> "def\n  some_method\n  \nother_method\nend"
    ```

## Regular Expressions

> Some people, when confronted with a problem, think
> "I know, I'll use regular expressions." Now they have two problems.<br/>
> -- Jamie Zawinski

* Don't use regular expressions if you just need plain text search in string:
  `string['text']`

* Don't use the cryptic Perl-legacy variables denoting last regexp group matches
  (`$1`, `$2`, etc). Use `Regexp.last_match[n]` instead.

    ```Ruby
    /(regexp)/ =~ string
    ...

    # bad
    process $1

    # good
    process Regexp.last_match[1]
    ```


* Avoid using numbered groups as it can be hard to track what they contain. Named groups
  can be used instead.

    ```Ruby
    # bad
    /(regexp)/ =~ string
    ...
    process Regexp.last_match[1]

    # good
    /(?<meaningful_var>regexp)/ =~ string
    ...
    process meaningful_var
    ```

* Be careful with `^` and `$` as they match start/end of line, not string endings.
  If you want to match the whole string use: `\A` and `\z` (not to be
  confused with `\Z` which is the equivalent of `/\n?\z/`).

    ```Ruby
    string = "some injection\nusername"
    string[/^username$/]   # matches
    string[/\Ausername\z/] # don't match
    ```

* Use `x` modifier for complex regexps. This makes them more readable and you
  can add some useful comments. Just be careful as spaces are ignored.

    ```Ruby
    regexp = %r{
      start         # some text
      \s            # white space char
      (group)       # first group
      (?:alt1|alt2) # some alternation
      end
    }x
    ```

## Percent Literals

* Use `%()`(it's a shorthand for `%Q`) for single-line strings which require both interpolation
  and embedded double-quotes. For multi-line strings, prefer heredocs.

    ```Ruby
    # bad (no interpolation needed)
    %(<div class="text">Some text</div>)
    # should be '<div class="text">Some text</div>'

    # bad (no double-quotes)
    %(This is #{quality} style)
    # should be "This is #{quality} style"

    # bad (multiple lines)
    %(<div>\n<span class="big">#{exclamation}</span>\n</div>)
    # should be a heredoc.

    # good (requires interpolation, has quotes, single line)
    %(<tr><td class="name">#{name}</td>)
    ```

* Avoid `%q` unless you have a string with both `'` and `"` in
  it. Regular string literals are more readable and should be
  preferred unless a lot of characters would have to be escaped in
  them.

    ```Ruby
    # bad
    name = %q(Bruce Wayne)
    time = %q(8 o'clock)
    question = %q("What did you say?")

    # good
    name = 'Bruce Wayne'
    time = "8 o'clock"
    question = '"What did you say?"'
    ```

* Use `%r` only for regular expressions matching *more than* one '/' character.

    ```Ruby
    # bad
    %r(\s+)

    # still bad
    %r(^/(.*)$)
    # should be /^\/(.*)$/

    # good
    %r(^/blog/2011/(.*)$)
    ```

* Avoid the use of `%x` unless you're going to invoke a command with backquotes in it(which is rather unlikely).

    ```Ruby
    # bad
    date = %x(date)

    # good
    date = `date`
    echo = %x(echo `date`)
    ```

* Prefer `()` as delimiters for all `%` literals, except `%r`. Since
  braces often appear inside regular expressions in many scenarios a
  less common character like `{` might be a better choice for a
  delimiter, depending on the regexp's content.

    ```Ruby
    # bad
    %w[one two three]
    %q{"Test's king!", John said.}

    # good
    %w(one tho three)
    %q("Test's king!", John said.)
    ```

## Metaprogramming

* Avoid needless metaprogramming.

* Do not mess around in core classes when writing libraries.
  (Do not monkey-patch them.)

* Avoid using `method_missing` for metaprogramming because backtraces become messy, the behavior is not listed in `#methods`, and misspelled method calls might silently work, e.g. `nukes.launch_state = false`. Consider using delegation, proxy, or `define_method` instead. If you must use `method_missing`:

  - Be sure to [also define `respond_to_missing?`](http://blog.marc-andre.ca/2010/11/methodmissing-politely.html)
  - Only catch methods with a well-defined prefix, such as `find_by_*` -- make your code as assertive as possible.
  - Call `super` at the end of your statement
  - Delegate to assertive, non-magical methods:

    ```ruby
    # bad
    def method_missing?(meth, *args, &block)
      if /^find_by_(?<prop>.*)/ =~ meth
        # ... lots of code to do a find_by
      else
        super
      end
    end

    # good
    def method_missing?(meth, *args, &block)
      if /^find_by_(?<prop>.*)/ =~ meth
        find_by(prop, *args, &block)
      else
        super
      end
    end

    # best of all, though, would to define_method as each findable attribute is declared
    ```

## Misc

* If you really need "global" methods, add them to Kernel
  and make them private.
* Use module instance variables instead of global variables.

    ```Ruby
    # bad
    $foo_bar = 1

    #good
    module Foo
      class << self
        attr_accessor :bar
      end
    end

    Foo.bar = 1
    ```

* Avoid `alias` when `alias_method` will do.
* Prefer `Time.now` over `Time.new` when retrieving the current system time.
* Do not mutate arguments unless that is the purpose of the method.

## Ruby on Rails

* When [converting](http://www.elabs.se/blog/36-working-with-time-zones-in-ruby-on-rails) `Date` to `Time` always use `#to_time_with_zone`.
* Always set `config.time_zone` in `application.rb`.

## Tools

Here's some tools to help you automatically check Ruby code against
this guide.

### RuboCop

[RuboCop](https://github.com/bbatsov/rubocop) is a Ruby code style
checker based on this style guide. RuboCop already covers a
significant portion of the Guide, supports both MRI 1.9 and MRI 2.0
and has good Emacs integration.

# License

![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png)
This work is licensed under a [Creative Commons Attribution 3.0 Unported License](http://creativecommons.org/licenses/by/3.0/deed.en_US)

Most of the work is done by [bbatsov](https://github.com/bbatsov).
