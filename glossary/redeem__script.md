<!-- TITLE: Redeem Script -->

The redeem script is a constraint script that is used in the P2SH payment mode.  The redeem script is provided by the spender's input script, however, it is chosen by the original spender.  This is possible because the output script constrains the redeem script's hash to match a specific value.  In P2SH this is the only constraint allowed by the output script, so the redeem script must implement all other constraints (such as signature checking).
