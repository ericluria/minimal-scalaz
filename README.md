// A demonstration of using scalaz.\/ (disjunctions) with flatMap and map,
// and how to sweeten things with for-comprehensions.

scala> import scalaz._
import scalaz._

/** Returns a disjunction based on the parity of the provided Int.
 *  @param error The message to show if the number is odd.
 *  @param number The number to test for even-ness.
 *  @return A right-disjunction containing the provided number if it's even;
 *          otherwise, a left-disjunction containing the provided error message.
 */
scala> def evenDisjunction(error: String, number: Int): \/[String, Int] = {
     |   if(number % 2 == 0) \/.right(number) else \/.left(error)
     | }
evenDisjunction: (error: String, number: Int)scalaz.\/[String,Int]

// If 2+3 is not even, we get a left-disjunction containing the error message.
scala> evenDisjunction("the sum of 2 + 3 is not even", 2 + 3)
res0: scalaz.\/[String,Int] = -\/(the sum of 2 + 3 is not even)

// If 2 is not even, we get a left-disjunction. But two is even! So we get a right-disjunction.
scala> evenDisjunction("the number '2' is not even", 2)
res2: scalaz.\/[String,Int] = \/-(2)

// Calling `flatMap` on a disjunction flatMaps the value on the right,
// The type of this inner anonymous function is `Int => \/[String, Int]`
// So the right-value of `res2` is our `x` parameter in the anonymous function.
scala> res2.flatMap(x => evenDisjunction("adding 2 yields an uneven number", x + 2))
res6: scalaz.\/[String,Int] = \/-(4)

// Now, using the syntactic sugar of the for-comprehension:
scala> for {
     |   two <- evenDisjunction("2 is uneven", 2)
     |   addTwo <- evenDisjunction("2 + 2 is uneven", two + 2)
     | } yield addTwo
res21: scalaz.\/[String,Int] = \/-(4)

// Similar to the example above, but now we get a left-disjunction since 2 + 3 is uneven.
scala> res2.flatMap(x =>
          evenDisjunction("adding 2 yields an uneven number", x + 2)
       ).flatMap(y =>
          evenDisjunction("adding 3 yields an even number", y + 3)
       )
res17: scalaz.\/[String,Int] = -\/(adding 3 yields an uneven number)

// Using syntactic sugar:
scala> for {
     |   two <- evenDisjunction("2 is uneven", 2)
     |   addThree <- evenDisjunction("2 + 3 is uneven", two + 3)
     | } yield addThree
res22: scalaz.\/[String,Int] = -\/(2 + 3 is uneven)
