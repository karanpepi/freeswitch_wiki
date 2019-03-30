### Freeswitch Issues
- Triggering calls beyond ~1000 with a + sign in the number results in buffer overflow in mod_enum (which is specific to libldns-dev being used by mod_enum), causing FreeSWITCH to crash. Profile being used in this case was Vodafone Transactional. Issue was rectified by substituting the +91 prefix with 0.