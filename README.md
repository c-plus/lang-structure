# Background
# I have an great interest in programming and I became a new programmer. 
# But programming is too unfriendly to an old guy.
# So I had a idea to create a new programming language for new programmer.
# This idea may be too crazy for me. But I like it.
# I call this programming language C+. Simple and safe. Combines C++ and rust.
# lang-structure
① Have "move" and "borrow" like rust. But only heap variables can be borrowed.
② Life time is simple. Only heap variables in upper and same hierarchy can be borrowed.
  You can't borrow money from your younger generation.
③ No direct pointer. But have smart pointer to visit members, base data units & bytes of data collection.
  like that: X[0], X[1], X[:0], X[:1], X[_0], X[_1]

