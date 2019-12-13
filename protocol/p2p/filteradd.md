<div class="cwikmeta">{
"title":"FILTERADD",
"related":["/protocol","/protocol/p2p/getdata","/protocol/p2p/filterload","/protocol/p2p/filterclear"]
}</div>

Add an entry into the installed bloom filter.

| compact int | N bytes | 
|-------------|---------|
|[vector](/protocol/p2p/vector) size N of| data

*data* is inserted into the bloom filter, exactly as if the [insert](https://github.com/BitcoinUnlimited/BitcoinUnlimited/blob/eb264e627e231f7219e60eef41b4e37cc52d6d9d/src/bloom.cpp#L116) operation had been called locally before sending the filter.