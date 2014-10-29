---
layout: post
title: "When it all clicked"
date: 2014-10-28
visible: false
---

<img src="/images/duck-rabbit-illusion.jpg" alt="Duck or Rabbit Illusion" style="width: 100%; margin: 0 auto" />

Take a look at the picture above. What do you see? I see a duck. I also see a
rabbit. Do you see them both?

Once you've seen both the duck and the rabbit, you can't go back. Your mind
will continue to see them both. No matter how hard you try to focus in on one
image, you will always see both a rabbit and a duck.

Learning is like this. Once you've 'truly' learned something, you can't go
back.  You can't forget it. With learning, there is usually a turning point, a
moment in time you can say that everything clicked. A moment when a duck turns
into a rabbit. A moment where everything that was previously unclear, becomes
clear and in focus. Throughout my software apprenticeship, there were several
such moments.

### Duck Typing & Interfaces

I don't have a degree in computer science or object oriented programming. I took
a few classes in Java but never practiced enough to grasp the key concepts. From
those classes, I remember the ideas of interfaces, polymorphism and duck typing,
but they never really stuck.

On one of my first projects, I was told to look at the Factory pattern. Without
understanding what was happening, I was able to implement it. Here's an example
from [FormsJs](https://github.com/bwvoss/forms.js):

{% highlight coffeescript linenos %}
_checkAllValidations: (validations, value) ->
  validationFactory = new FormsJs.Validator.Factory
  _.all validations, (validator) =>
  validationFactory.build(validator).isValid(value, @scope)
{% endhighlight %}

The `_checkAllValidations` function takes an array of validations and a value to
check against. For each validation, it builds a validator and calls
`isValid()` on it. Here's what a validator might look like:

{% highlight coffeescript linenos %}
class FormsJs.Validator.Email

  EMAILREGEXP: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i

  constructor: (@options) ->

  isValid: (value) ->
    @EMAILREGEXP.test(value) or value is ''
{% endhighlight %}

The validator is a duck type. As long as it responds to the `.isValid()` method
it can be called a validator. We can create any number of validators (like
minLength, required, matchesInput) and pass them into the `_checkAllValidations`
function and they will work. This gives us the flexibility to have multiple
validators (each with different functionality), but access them all through just
one function. It creates a flexible system that can grow as needed in the future
and doesn't contain a bunch of `if..else` statements.

At this point, I understood the factory pattern, but it was difficult to apply
it in other settings. I certainly didn't make the connection to duck typing.

That all changed when I built an HTTP server in Java. Ironically, using a
statically typed language, I finally had to make everything explicit. I had to
create interfaces.

{% highlight java linenos %}
public interface Handler {

  public Response handle() throws IOException;

}
{% endhighlight %}

Each `Handler` object responded to `handle()` and returned a `Response`. It was
at this point that I made the connection to the factory pattern and duck types.

Once I made that connection, I also immediately understood what mocks were for
in testing. A mock is nothing more than a duck type. It is meant to
replace the real object so that the test doesn't depend on anything concrete.
Mocks are especially handy for input and output:

{% highlight java linenos %}
public class MockClientStreams {
    private InputStream inputStream;
    private OutputStream outputStream;

    public MockClientStreams(byte[] data) {
        inputStream = new ByteArrayInputStream(data);
        outputStream = new ByteArrayOutputStream();
    }

    public InputStream getInputStream() {
        return inputStream;
    }

    public OutputStream getOutputStream() {
        return outputStream;
    }
}
{% endhighlight %}

In this system, `InputStream` and `OutputStream` are the interfaces. We
can mock them with an in-memory byte array so that our tests don't wait for
input from us to run. When the actual server runs it can use the real input and
output streams.

Learning about interfaces and duck typing has changed the way I approach writing
code. It's easier for me to see how objects interact and fit together and I am
better able to test things that may at first seem difficult to test. While duck
typing is quite useful on its own, once I incorporated it into my code, I
solidified another concept that had thus far eluded me.

### Abstraction

Some of the first things my mentor told me were "encapsulate what changes" and
"avoid leaky abstractions". I nodded in understanding, but had no idea what he
was talking about. It wasn't until I built the standard unbeatable tic-tac-toe
game for the third time that I fully grasped the ideas behind abstraction.

Typically, one implements the tic-tac-toe board as an array. That array is
usually given an initial count, like 9, to represent a 3x3 grid. Depending on
the language you choose, the array is usually filled with something. In Ruby,
it's `nil`.

{% highlight ruby linenos %}
board = Array.new(9)
=> [nil, nil, nil, nil, nil, nil, nil, nil, nil]
{% endhighlight %}

When a move is made, that player's symbol is placed at that spot in the array
(don't forget arrays are 0 based).

{% highlight ruby linenos %}
board[1] = "x"
=> [nil, "x", nil, nil, nil, nil, nil, nil, nil]
{% endhighlight %}

As you start to put your game together, you realize that accessing the array
directly will make it difficult to change later - say you want to use a map
instead. This is your opportunity to encapsulate and abstract the board. You
create a board class:

{% highlight ruby linenos %}
class Board
  attr_reader :state

  def initialize(size)
    @state = Array.new(size)
  end

  def make_move(cell, symbol)
    state[cell] = symbol
  end
end
{% endhighlight %}

Now you can create a new board with a given size and update the board with the
`make_move()` method. At this point your board class starts to come together and
you've successfully abstracted the array into a board. If you ever change the
array, the rest of your system doesn't care because it's accessing the board
through the given interface.

This was enough to solidify the concept of abstraction for me. However, it still
never occurred to me how this abstraction could leak. Then I wrote the following
to validate user input:

{% highlight ruby linenos %}
if board.state[choice].nil?
  choice
else
  get_move(board, opponent, error_message)
end
{% endhighlight %}

At this point it became clear to me how an abstraction can leak. Because I knew
that the board was an array, and empty cells were nil, I could validate the
input of the user by directly accessing the array. But if anything changed about
my implementation of the board, this method would break. What if I wanted to use
empty strings instead of nil as placeholders in the array?

The board should be the only object that knows what cells are empty. To allow
other objects to ask this question, we create a method called
`available_cells()` that returns an array of numbers coresponding to the
available cells. Then I can write my validation like this (I actually used a
ternary operator, but wanted to be explicit here):

{% highlight ruby linenos %}
if board.available_cells.include?(choice)
  choice
else
  get_move(board, opponent, error_message)
end
{% endhighlight %}

By making that mistake, the idea of a leaky abstraction became clear. I
understood what my mentor meant and I now know what to look for so as not to
make that mistake again. Your abstracted object should be the only one that
knows about its inner structure.

What's nice about both interfaces and abstraction is that they complement each
other quite well. If you want to test whether you have a clean abstraction, see
if your object can be replaced by a mock object and still function. If
you have clean boundaries and well defined interfaces, it should be trivial to
swap objects.

Learning is difficult work, and it doesn't always happen when you want it to.
But when it does happen, the feeling is magical. In an instant, you see the
world differently. Everything becomes clearer. Ducks (and their types) turn into
rabbits.
