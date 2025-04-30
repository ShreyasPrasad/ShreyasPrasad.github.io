### Why would anyone name a language P(ee)?

I recently learned [the P language](https://github.com/p-org/P) (yeah I know that sounds funny but it's real I promise). Like Alloy, it's a language used for formal verification but it's imperative instead of declarative. 

That is, instead of defining how you know a certain outcome was reached, you define all outcomes (states) and the transitioning events between them. P then attempts to explore all possible permutations of events, validating that your invariants hold. 

> [!NOTE] Installing P
> P can be installed [here](https://p-org.github.io/P/getstarted/install/). I recommend using the Peasy VSCode extension when working with P. It provides syntax highlighting and autocomplete.

### Will the Maple Leaf's get reverse swept by the Senators?

P revolves around defining state machines and communicating between them using events. You can think of everything in life as some sort of machine. You are a machine that receives some inputs (food, sleep, exercise, cocaine, etc) and projects events out to the rest of the world (speaking to others, dancing, travelling, etc). 

Here's a really simple example that showcases a lot of what P offers and helps me confront my lifelong Maple Leafs trauma. 3-0 is never enough.

```
// Event definitions
event MapleLeafsWin;
event SenatorsWin;

machine MapleLeafs {
  start state Init {
    entry {
      // Send 4 MapleLeafsWin events to World
      send World, MapleLeafsWin;
      send World, MapleLeafsWin;
      send World, MapleLeafsWin;
      send World, MapleLeafsWin;
    }
  }
}

machine Senators {
  start state Init {
    entry {
      // Send 4 SenatorsWin events to World
      send World, SenatorsWin;
      send World, SenatorsWin;
      send World, SenatorsWin;
      send World, SenatorsWin;
    }
  }
}
```

First, we define two events. The `MapleLeafs` machine uses the `MapleLeafsWin` event to tell the world that it won a game. The `Senators` machine does the same with `SenatorsWin` events. Both machines initialize by entering the state designated as start and running the code defined in the `entry` block. This is just like a  constructor in other languages.

The `send` keyword sends a particular event, optionally with a payload to another machine. Both machines send 4 events using P's `send` keyword to the `World` machine, that we have not yet defined. We choose 4 events because this is the required number of wins to win a best-of-7 playoff series. Now, let's define the `World` machine.

```
machine World {
  var mapleCount: int;
  var sensCount: int;

  start state Init {
    on MapleLeafsWin do {
      mapleCount = mapleCount + 1;
      if (mapleCount == 4) {
	    print "Maple Leafs won the series!";
        raise halt;
      }
    }

    on SenatorsWin do {
      sensCount = sensCount + 1;
      if (sensCount == 4) {
        print "Senators won the series :(";
        raise halt;
      }
    }
  }
}
```

The `World` machine listens for events of either type using the `on` keyword. When it eventually receives 4 events of either type, it indicates a winner and halts execution using the `raise` keyword - similar to throwing a terminating exception in other languages. Now let's define an `Orchestrator` machine that initializes all of these machines, kicking everything off.

```
machine Orchestrator {
  start state StartState {
    entry {
      var world: World = new World();
      var leafs: MapleLeafs = new MapleLeafs();
      var senators: Senators = new Senators();
    }
  }
}
```

### Non-determinism in P

The cool thing is, the order of event delivery is random and determined by P at runtime using a seed. This means that the `World` machine can see events in any order from the other machines. So this situation models all possible series winning combinations of either team and the `World` machine can indicate either team winning based on the sequence of events P decides it receives. 

### Final Thoughts

P is much higher level than Alloy, making it easier and more intuitive to express state-based situations. Plenty of teams at AWS, [such as S3 and DynamoDb](https://queue.acm.org/detail.cfm?id=3712057), use P to model their distributed systems and ensure that important properties, like data durability, are always satisfied. 

Again, like Alloy, P has not seen widespread adoption because of the difficulty in creating and maintaining a model. Ensuring that you model the right things and that your real code does not significantly diverge from your model is hard for teams that are already resource constrained and don't see correctness verification as an immediate priority.

