
# Difficulty Adjustment Algorithm

In order to correct for changes in the Bitcoin Cash network's total hashing power (i.e. as hardware improves or nodes are added to or removed from the network), the amount of work required to mine a block must change in tandem.
Bitcoin Cash solves this by adjusting the difficulty according to an algorithm that looks at recent block timestamps, infers the hashing power that led to those timestamps, and adjusts the difficulty for future blocks accordingly.

The current Bitcoin Cash difficulty adjustment algorithm attempts to ensure that the difficulty of new blocks is always closely tied to recent block difficulties.
Put into place as a part of [HF-20171113](/protocol/forks/hf-20171113), it performs the following calculation to determine the difficulty of a block, <code>B<sub>n+1</sub></code>, with block height `n+1`:

 - Select <code>B<sub>new</sub></code>: The block with the median timestamp of the blocks <code>B<sub>n</sub></code>, <code>B<sub>n-1</sub></code>, and <code>B<sub>n-2</sub></code>
 - Select <code>B<sub>old</sub></code>: The block with the median timestamp of the blocks <code>B<sub>n-144</sub></code>, <code>B<sub>n-145</sub></code>, and <code>B<sub>n-146</sub></code>.
 - Calculate `t`, the difference between the timestamps of <code>B<sub>new</sub></code> and <code>B<sub>old</sub></code>.  If this difference is less than `72 * 600`, use `72 * 600`, if it is above `288 * 600`, use `288 * 600`.
 - Calculate `W`, the difference in [chainwork](/protocol/blockchain/proof-of-work#chainwork) between <code>B<sub>new</sub></code> and <code>B<sub>old</sub></code>.
 - Calculate `PW`, the projected work for the next block, as `(W * 600) / t`.
 - Calculate `T`, the target difficulty, as <code>(2<sup>256</sup> - PW) / PW</code>.  In 256-bit two's-complement arithmetic, this is equivalent to `(-PW) / PW`.
 - If `T` is greater than the maximum target of `0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF`, use the maximum target.  Otherwise, use `T`.

## Legacy Difficulty Adjustment Algorithm

Prior to [BCH-UAHF](/protocol/forks/bch-uahf), the original Bitcoin difficulty adjustment algorithm was used.
To determine the difficulty of a block, <code>B<sub>n+1</sub></code>, with block height `n+1`:

 - If the `n` (or the current block height) is **not** divisible by 2016, use the target of the last block, B<sub>n</sub>.  Otherwise, continue.
 - Get the 2015th ancestor of the last block, B<sub>n-2015</sub> (this was originally intended to be the 2016th ancestor but due to a bug in the original implementation, the 2015th was used instead).
 - Get the last block, B<sub>n</sub>.
 - Calculate `t`, the difference between the timestamp of B<sub>n</sub> and B<sub>n-2015</sub>.
 - Calculate `c`, the correction factor by dividing `t` by the number of seconds in two weeks (`2 * 7 * 24 * 60 * 60 = 1,209,600`).  Note this is the expected time for 2016 blocks at a rate of one block every ten minutes (`2016 * 10 * 60 = 1,209,600`). If the result is less than `0.25`, use `0.25`.  If it is greater than `4`, use `4`.
 - Calculate the new target, `T`, by multiplying the target of B<sub>n</sub> by `c`.
 - If `T` is greater than the maximum target of `0x00000000FFFF0000000000000000000000000000000000000000000000000000`, use the maximum target instead.  Otherwise, use `T`.

## Emergency Difficulty Adjustment Algorithm

As a part of BCH-UAHF, due to the anticipated decrease in hashing power, the following adjustment to the legacy difficulty adjustment algorithm above was put into place:

 - ...

This algorithm was superseded by the current difficulty adjustment algorithm as a part of HF-20171113.