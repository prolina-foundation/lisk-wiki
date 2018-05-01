There are currently 5 types of forks known in Lisk:

## Fork type 1

Client receives block with expected height (last height + 1) but the
block's parent ID is different from the last block known to the client.

**Resolution:** Discard the incoming block. If incoming block older than the last block of the local chain,
remove the last two local blocks.

## Fork type 2

An incoming block contains a transaction that was already confirmed by
another block.

**Resolution:** Discard the incoming block.

## Fork type 3

A mismatch between the expected public key and the public key used to sign
the incoming block.

**Possible cause:** The block was not forged by the right delegate

**Resolution:** Discard the block

## Fork type 4

No information were found about this fork type. It may not exist.

## Fork type 5

An incoming block has the same block height and the same parent ID, but a
different block ID than the last local block.

**Possible cause:** Double forging

**Resolution:**

* If incoming block is newer than the last local block, discard incoming block
* Otherwise, remove last local block and replace with incoming block

This resolution strategy is still in progress, see
[Fix fork cause 5](https://github.com/LiskHQ/lisk/issues/402)

## Operations

### B beats A

For two blocks different blocks, there is a strict total order `beats`,
where `B beats A` means that block B will remain in the blockchain
and A is dropped.

`B beats A` is implemeted as follows

```javascript
function bBeatsA(a, b) {
    return a.timestamp > b.timestamp
        || (a.timestamp == b.timestamp && a.id > b.id)
}
```

Note: `a.id > b.id` is defined on JavaScript strings, so this is
string comparison. There is no semantic meaning in this part. It just
ensures that for two different blocks, one is always defined as newer.

For two different blocks A, B either `A beats B` or `B beats A`.

The relation is transitive: `C beats B` and `B beats A` implies `C beats A`.
