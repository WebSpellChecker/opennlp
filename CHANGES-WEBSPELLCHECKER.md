# WebSpellChecker changes for Apache OpenNLP

This document records WebSpellChecker-specific changes on top of upstream
Apache OpenNLP 1.9.4. This fork exists to backport security fixes from
later upstream versions onto the 1.9.4 baseline, because upgrading to
opennlp-tools 2.x is not viable for our use case.

The fork is published as `com.webspellchecker:opennlp-tools` with version
suffix `-webspellchecker-N` for each iteration.

## 2026-05-29 (1.9.4-webspellchecker-1)

### Security
- **CVE-2026-40682 (XXE in DictionaryEntryPersistor):** backported XML parsing
  hardening from upstream PR #1020. XmlUtil.createSaxParser and
  XmlUtil.createDocumentBuilder now disable DOCTYPE, external entities, and
  external DTD/schema. DictionaryEntryPersistor routes through XmlUtil
  instead of XMLReaderFactory.
- **CVE-2026-42027 (Unsafe reflection in ExtensionLoader):** backported
  allowlist mechanism from upstream PR #1021. ExtensionLoader.instantiateExtension
  now rejects class names whose package prefix is not in an allowlist
  (default: `opennlp.`), configurable via the OPENNLP_EXT_ALLOWED_PACKAGES
  system property or ExtensionLoader.registerAllowedPackage.
- **CVE-2026-42440 (DoS in AbstractModelReader):** backported bounds-check
  from upstream PR #1022. getOutcomes, getOutcomePatterns, getPredicates
  now reject counts that are negative or exceed MAX_ENTRIES (default
  10,000,000, configurable via the OPENNLP_MAX_ENTRIES system property).

### Build infrastructure
- **maven-bundle-plugin upgrade:** bumped 4.2.1 → 5.1.9 to fix
  ConcurrentModificationException on JDK 11+. Plugin only writes OSGi
  manifest metadata; no bytecode impact.
- **LF line endings:** added .gitattributes (`* text=auto eol=lf`) to
  enforce LF repo-wide regardless of each developer's `core.autocrlf`
  setting. Without this, Windows checkouts get CRLF, which breaks
  Checkstyle and the Brat format tests.

### Release engineering
- **Artifact rename:** changed Maven coordinates from
  `org.apache.opennlp:opennlp-tools:1.9.4` to
  `com.webspellchecker:opennlp-tools:1.9.4-webspellchecker-1`. The new
  groupId removes any collision with upstream Maven Central, and the
  `-webspellchecker-N` qualifier allows iteration on future CVE patches
  with proper Maven version ordering.
