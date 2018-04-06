A forging round is a sequence of 101 forging slots, one per active delegate. Each of those slots is open for 10 seconds, so a round takes 101 * 10 seconds (~ 17 minutes). However, a round is defined by the height of the blocks forged in it, not by any time on the clock.

The first forging round consisted of the blocks 1…101, the second of the blocks 102…202 and so on. In general, a forging round `N` ends at height `101*N` and starts at height `101*N-100`. To obtain a round number from a block's height, you have the formula

```javascript
function forgingRound(height) {
    // See https://github.com/LiskHQ/lisk-explorer/blob/development/src/filters/round.js
    return Math.ceil(height / 101);
}
```

Since round number and the position within a round can be calculated from the block height, the round number is not stored separately.

The order of forging delegates is fixed within a given round. After each round, active delegates are shuffled, such that the order is different in the next round.