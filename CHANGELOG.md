# Changelog

## [0.23.0](https://github.com/Omnidim/skills/compare/v0.22.0...v0.23.0) (2026-11-28)


### Added

* add troubleshoot playbooks for calls, audio, billing, numbers, WhatsApp, dashboard, referrals and feature requests ([b443a3b](https://github.com/Omnidim/skills/commit/b443a3be5f17681a5bae034f525105c9397ba95a))
* troubleshoot reads a call step by step with getCallEvents ([67af2e8](https://github.com/Omnidim/skills/commit/67af2e8b2555e052113027b8cf6101574d10a3e3))
* troubleshoot reads a call step by step with getCallEvents ([d03c522](https://github.com/Omnidim/skills/commit/d03c52286fdc86c174c16acc8da80f73776a74e7))
* troubleshoot says what no tool can show instead of guessing ([c6adf34](https://github.com/Omnidim/skills/commit/c6adf3422b7a0e640688eefcbabdabb8b1aa6b67))
* troubleshoot skill finds what changed and proves it with call ids ([be0a2ec](https://github.com/Omnidim/skills/commit/be0a2ec90f56f10eb716f38482be263c860b3810))
* troubleshoot skill finds what changed and proves the cause with call ids ([8325f2c](https://github.com/Omnidim/skills/commit/8325f2ccd3bddb71ea6be137b614be1992b12b49))


### Fixed

* **build-agent:** new agents start at interruption_min_words 3 with advanced noise reduction ([49bd761](https://github.com/Omnidim/skills/commit/49bd761b9ecc1f3a1299d2da9c5d8536623f7541))
* **build-agent:** new agents start at interruption_min_words 3 with advanced noise reduction ([7ccd0fe](https://github.com/Omnidim/skills/commit/7ccd0feac007b68a3e8dd57ec10eb07a25de1317))
* troubleshoot playbooks name a real hand-off and keep the dashboard playbook brand-neutral ([4a11afb](https://github.com/Omnidim/skills/commit/4a11afb0e96821d9c9070e08f6dcaa9efe48786e))

## [0.22.0](https://github.com/Omnidim/skills/compare/v0.21.0...v0.22.0) (2026-11-26)


### Added

* add the ChatGPT plugin manifest, listing and review test cases ([#14](https://github.com/Omnidim/skills/issues/14)) ([8c39d2d](https://github.com/Omnidim/skills/commit/8c39d2d3c77150b71c22f1a63bb0c16fe8fc927c))
* **agent-prompt:** give each agent a kind, so only sales and collections agents keep pushing after a no ([#18](https://github.com/Omnidim/skills/issues/18)) ([c3ec70e](https://github.com/Omnidim/skills/commit/c3ec70e081590e20fb6e3277af985989cadd92ed))
* name the merged workflow tools (workflow, workflowActions) ([e3a99b1](https://github.com/Omnidim/skills/commit/e3a99b1e961c5f2aee5e4e04b20846b41976c4dd))
* name the merged workflow tools (workflow, workflowActions) ([bfb745f](https://github.com/Omnidim/skills/commit/bfb745f6786ddc00abbae2ea1e7fee9bcb1b77b1))


### Fixed

* describe outbound calls in the ChatGPT listing as calls to the customer's own lists ([#16](https://github.com/Omnidim/skills/issues/16)) ([f1211d0](https://github.com/Omnidim/skills/commit/f1211d0d053d66790c10c213ae86dc637e4fc9a2))
* leave the campaign skill out of the ChatGPT ZIP and match the agent test case to real behavior ([#15](https://github.com/Omnidim/skills/issues/15)) ([04cffbc](https://github.com/Omnidim/skills/commit/04cffbc7dbc9137625e805e6dd127a35d468ea48))
* name each workflow tool on its own in the workflow playbook and guide ([#17](https://github.com/Omnidim/skills/issues/17)) ([2994fd7](https://github.com/Omnidim/skills/commit/2994fd7dca50302af3c5861af6d964d76cfc11fe))
* point the skills at the MCP setup docs instead of the omnidim-connect skill ([#11](https://github.com/Omnidim/skills/issues/11)) ([ce71fca](https://github.com/Omnidim/skills/commit/ce71fca4a2c87730bb852eeec8fa219c076d668e))

## [0.21.0](https://github.com/Omnidim/skills/compare/v0.20.0...v0.21.0) (2026-11-24)


### Added

* name the merged MCP tools in the playbooks and guides ([1789389](https://github.com/Omnidim/skills/commit/178938917d6daa966eada1d3e138127ba4b3d646))
* name the merged MCP tools in the playbooks and guides ([4dbe9b5](https://github.com/Omnidim/skills/commit/4dbe9b54c9ce046f00adc9b5f4cddf2534812e07))

## [0.20.0](https://github.com/Omnidim/skills/compare/v0.19.1...v0.20.0) (2026-11-24)


### Added

* add the omnidim-client-onboarding skill for resellers ([#3](https://github.com/Omnidim/skills/issues/3)) ([65ff7e4](https://github.com/Omnidim/skills/commit/65ff7e43b5b9338799ef2abc82eb2e3fa9697ab7))
* first five OmniDimension agent skills ([f80190e](https://github.com/Omnidim/skills/commit/f80190e03e89de8e6f221b243c394b8fede54d15))
* install the skills as a plugin in claude.ai ([#6](https://github.com/Omnidim/skills/issues/6)) ([72a6b9b](https://github.com/Omnidim/skills/commit/72a6b9b5e25275652955a3f5b8f28af7f844db90))
* name the skills omnidim-*, add tips measured on real calls ([a193267](https://github.com/Omnidim/skills/commit/a19326701cd99e8ce389c863041521a1780a3d71))
* put skills under skills/, add a one-step Claude Code plugin, and a README that starts with how to install ([dbaec98](https://github.com/Omnidim/skills/commit/dbaec98c26adf4b6398cd22042e5e1279e361a57))
* write each playbook and guide here, once ([#7](https://github.com/Omnidim/skills/issues/7)) ([e231373](https://github.com/Omnidim/skills/commit/e2313732a35004f20ce96c49a776f5bd7242a624))


### Fixed

* say where each tip comes from, name skills instead of MCP prompts ([af58fea](https://github.com/Omnidim/skills/commit/af58fea882c41b786ce47b92eb992178701b7c65))
