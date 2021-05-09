# Economy Specification
### Summary

The Uchuumaru economy is strongly inspired by Zuramaru and Kanchou's internal systems. The economy will be used for a multitude of functionalities within Uchuumaru but should remain separate from the primary moderation architecture. It must therefore expose a clean, simple-to-use, and `Result` callback-based API. 

### Goals
The overarching intention of the economy is to provide a sense of reward and competition with users. Collecting currency should be tedious, but the interface to do so should also invoke feelings of satisfaction and achievement. Furthermore, the economy should be strictly moderated to ensure inflation does not occur and that a small handful of users do not have an unreasonable amount of currency relative to the little time they put into farming (see `?hanalb`). And finally, the economy should ideally be tightly coupled with the server as a whole, rather than being perceived as "belonging to Uchuumaru." 