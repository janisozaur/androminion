Discussions and decisions around the 'Quick Play' feature

## High level granular ideas ##
  1. Auto Pick Treasure
  1. Auto Trash
  1. Auto Discard
  1. Auto Buy

## Specific granular ideas ##
  1. Trash one or more cards (Chapel, Trade Route, Trading Post). -- done
    * The way this is implemented now seems fine.
  1. Discard one or more cards (Militia, Warehouse, Young Witch). -- done
    * The way this is implemented now seems fine.
  1. Trash or discard one or more cards for an effect based on the cards trashed (Apprentice, Cellar, Forge, Remodel) -- done
    * I don't think Autoplay should handle this case.
  1. Gain/Buy a card (Workshop) -- done
    * I don't think Autoplay should ever handle this case.  Once you start automatically deciding which cards to gain, you might as well be writing a bot.
  1. Cards with multiple modes (Pawn, Steward, Trusted Steed) -- done
    * I don't think Autoplay should ever handle this case.
  1. Deciding whether to play an Action card
    * Action cards that do not have multiple modes and do not cause the player to trash or discard any cards can be autoplayed.  If the player has any Action cards that give +1 action (or +2), autoplay them first.  Next, if the player has a number of Action cards less than or equal to their available number of actions, play those Action cards.  When playing Action cards, first play any cards that cause the player to draw more cards.
  1. Deciding whether to use a reaction
    * As stated in [issue 48](https://code.google.com/p/androminion/issues/detail?id=48), reactions should be optional.  For the most part, though, an option to autoplay reactions would be fine.

## Cards Ideas ##
**_Forge/Remake/Apprentice/Etc_** -- done

Remodel and Expand have already been set not to autoplay ( [issue 6](https://code.google.com/p/androminion/issues/detail?id=6) ,  [issue 15](https://code.google.com/p/androminion/issues/detail?id=15) ), so it makes sense that these wouldn't either, since they all give a variable benefit based on the cost of the trashed card(s).

**_Minion_**

I have no idea how QP decides which thing to do.  It has discarded my amazing hand which contained, among other things, more Minions.  It has also given me +2 coin when the rest of my hand was more or less garbage.


---

Here is the code, should we fix this, or just turn off QP for minion?
```
    public MinionOption minion_chooseOption(MoveContext context) {
        if (context.getCoinAvailableForBuy() >= 5) {
            return Player.MinionOption.AddGold;
        }

        if (getHand().size() <= 3) {
            return Player.MinionOption.RolloverCards;
        }
        return Player.MinionOption.AddGold;
    }
```

---


**_Native Village_** -- done (QP only when the mat is empty)

Give me back my cards!  Pretty please?  (The only time this is a mostly obvious decision is when there are no cards on the Native Village mat.  In my testing game, it was maddening to watch more and more good cards pile where I couldn't get to them.)

**_Pawn_** -- done (turned off)

I don't know how QP decides what to do with this card either, and the choices often don't make sense.  For example, sometimes QP chooses +1 Action when I have no other action cards.  It also does not seem possible to choose +1 Buy.

**_(Discarding in general)_**

It seems like QP will always choose to discard Curse/Copper/victory point cards before discarding anything else such as extra actions or potions.  This is often not the best decision.

**_Chapel_** -- done (turned off)

The QP version of this card seems to be "trash all curses, coppers, and estates from your hand".  Sometimes I don't want to trash all the copper in my hand.  Sometimes I want to trash other cards (e.g. Potion, Sea Hag, cards someone gave me with Swindler/Ambassador/Masquerade, etc).

**_Torturer_**

This card features all of the problems involved with discarding in general.  In addition, QP makes a lot of questionable decisions about when to discard and when to take the curse.

**_Mining Village_**

This has already been reported in  [issue 30](https://code.google.com/p/androminion/issues/detail?id=30) , but I have also seen QP trash a Mining Village when I didn't really want to do so.

**_Grand Market_**

QP will always play all the copper from your hand, which can be unwanted when Grand Market is available.

**_Swindler_**

QP makes inconsistent decisions about what cards to give to opponents.

**_Hamlet_**

There are a couple of reasons why current Hamlet logic should change.
  1. Sometimes I want to discard a card for +1 Buy.  The current logic does not appear to allow this.
  1. Sometimes I want to discard a non-trash card for +1 Action.  For example, if my hand after playing Hamlet is [Library, Library, Copper, Copper, Copper] I might want to ditch the extra Library instead of a Copper.
  1. Sometimes trash cards aren't trash.  If I had the hand [Lighthouse, Lighthouse, Baron, Estate, Silver], I definitely wouldn't want to trash the Estate.
  1. Sometimes I want to get rid of specific cards.  With the hand [Menagerie, Menagerie, Explorer, Explorer, Gold] I might want to trash an Explorer to power up my Menageries.

Just about the only automatic logic that I think would work would be something like this.

For the first choice:
  1. If the player has no Action cards, do not discard a card for +1 Action.  Otherwise, ask what to do.

Then, for the second choice:
  1. If the player has no Action cards and at least 1 trashCard, discard the trashCard for +1 Buy.  Otherwise, ask what to do.