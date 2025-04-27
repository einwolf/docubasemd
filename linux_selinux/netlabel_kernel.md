# Netlabel in Linux kernel

## Netlabelctl interface

https://github.com/torvalds/linux/blob/v6.14/net/netlabel/netlabel_unlabeled.c
https://github.com/torvalds/linux/blob/v6.14/net/netlabel/netlabel_cipso_v4.c
https://github.com/torvalds/linux/blob/v6.14/net/netlabel/netlabel_calipso.c

## CIPSO 2.2

The code tries to match sensitivities and categories to DOI interpretation
through CIPSO configuration. It errors if it can't do that.

CIPSO type 1 in passthrough mode category > 239 can't map to DOI values.
For trans modes it can take anything that is valid.

Generally, it appears to take the category in low to high (type 1 or 2) or
high to low (type 5) and tries to add category or category range one at a time.
It errors when the header gets larger than the IP header size limit.

THe CIPSO type 1 error isn't a "category number too big" error.
It's a "tried to add 241" categories error.

https://github.com/torvalds/linux/blob/v6.14/net/ipv4/cipso_ipv4.c

Type 1 tag
Created in cipso_v4_gentag_rbm().
Parsed in cipso_v4_parsetag_rbm().

Type 2 tag
Created in cipso_v4_gentag_enum().
Created in cipso_v4_parsetag_enum().

cipso_v4_map_lvl_ntoh - Perform a level mapping from the network to the host

cipso_v4_map_cat_rbm_hton - Perform a category mapping from host to network
cipso_v4_map_cat_rbm_ntoh - Perform a category mapping from network to host

## CALIPSO

https://github.com/torvalds/linux/blob/v6.14/net/ipv6/calipso.c
