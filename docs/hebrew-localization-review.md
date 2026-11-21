# MailCraft Hebrew localization — review and QA guide

This document gives reviewers a **MailCraft-specific** checklist for Hebrew interface translations and right-to-left behavior. It does not claim that full RTL layout is implemented or has been tested.

The language catalog proposed in [MailCraft Hebrew UI PR #2](https://github.com/artcraft-candidates/mailcraft/pull/2) is AI-assisted and independently written from MailCraft's **English** UI strings. The proposed Hebrew terminology and visual behavior still need native-speaker and UI review. Do not copy wording from the installation or localization files of another email client.

## Scope

- Source catalog: crates/ui-egui/src/i18n/es.tsv or another existing catalog for the **English source keys only**.
- Proposed target: crates/ui-egui/src/i18n/he.tsv.
- Catalog format: context, English source, Hebrew translation separated by TAB characters.
- Keep placeholders such as {name}, {n}, {email}, {sender} and {error} unchanged.
- Preserve command IDs, CLI options, email addresses, server names, file names, protocol identifiers, shortcuts and plain user content.
- Keep trailing ellipses (…) and escaped newlines/tabs in their original functional positions.
- Every interface key must have a translation in a registered catalog; avoid silently weakening the coverage test.
- Do not insert bidi override controls to force visual order in the catalog. Fix visual direction in the layout or text-rendering layer.

## Proposed Hebrew terminology

These are **draft translation choices**, not a completed human terminology review. Context matters: the same English word can name an action or a folder.

| English source | Proposed Hebrew | Context |
|---|---|---|
| Inbox | דואר נכנס | Folder |
| All Inboxes | כל תיבות הדואר הנכנס | Combined view |
| Sent Items | פריטים שנשלחו | Folder |
| Drafts | טיוטות | Folder |
| Outbox | דואר יוצא | Offline sending |
| Deleted Items | פריטים שנמחקו | Folder |
| Junk Email | דואר זבל | Folder |
| Archive | העברה לארכיון | Command |
| Archive (folder context) | ארכיון | Folder |
| Delete | מחיקה | Command |
| Remove | הסרה | UI object |
| Reply | תשובה | Command |
| Reply all | תשובה לכולם | Command |
| Forward | העברה הלאה | Mail command, distinct from moving a message |
| Move | העברה | Folder operation |
| Attach file | צירוף קובץ | Compose |
| Draft saved | הטיוטה נשמרה | Confirmation |
| Send | שליחה | Compose |
| Send / Receive | שליחה / קבלה | Sync |
| Unread | לא נקראו | Filter |
| Flag | סימון בדגל | Command |
| Unflag | הסרת דגל | Command |
| Pin | נעיצה | Command |
| Unpin | ביטול נעיצה | Command |
| Snooze | דחיית טיפול | Message reminder |
| Focused Inbox | תיבת דואר נכנס ממוקדת | Mail view |
| Other | אחר | Mail view |
| Cc | עותק | Recipients |
| Bcc | עותק מוסתר | Recipients |
| Signature | חתימה | Compose |
| Rules | כללים | Automatic sorting |
| Search folders | חיפוש תיקיות | Navigation |
| Search people | חיפוש אנשים | Contacts |
| Calendar | לוח שנה | App area |
| Contacts / People | אנשי קשר / אנשים | Address book / navigation |
| Automatic replies | תשובות אוטומטיות | Account settings |
| Server settings | הגדרות שרת | Account settings |
| Privacy | פרטיות | Preferences |

The English token used by the program is not always the string the person sees. Review the source and its actual UI context before deciding whether an action is a noun or a verb.

## Manual visual testing before qualifying Hebrew as an RTL interface

The current [gaps list](gaps.md) identifies RTL and bidirectional text as unfinished work. Adding a TSV catalog alone does **not** prove RTL readiness.

| Surface | Scenarios to test | Typical failure to watch for |
|---|---|---|
| Language picker | Manual selection of עברית; Windows he-IL; Linux he_IL.UTF-8; legacy iw | Hebrew not selected or wrong fallback |
| Folder navigation | Inbox, nested folder names, unread badges, expanded tree | Reversed icon/badge order or truncation |
| Message list | Hebrew subjects, Latin sender names, timestamps, flags | Wrong reading order for mixed text |
| Reading pane | Hebrew paragraph, quoted English message, phone number and URL | Punctuation or inline runs displayed backward |
| Compose | To, Cc, Bcc, subject and message body with mixed scripts | Cursor movement, selection, wrapping, paste |
| Attachments | Hebrew file name with Latin extension, e.g. דוח-2026.pdf | Extension or download name reordering |
| Search | Latin operators from:, subject:, has:attachment with Hebrew search terms | Operators interpreted as Hebrew, broken cursor |
| Rules | Folder selection and dynamic {folder} / {sender} values | Mixed-direction placeholder positioning |
| Calendar | Month/day labels, range strings, mixed Hebrew and numeric dates | Date sequence or punctuation reordered |
| Account setup | user@example.com, IMAP/SMTP ports, error messages | Credentials or hostnames visually corrupted |
| Accessibility | Screen reader labels, focus traversal and keyboard shortcuts | Visual and logical order disagree |
| Platforms | Windows, macOS, Linux; desktop and web sample mailbox | Hebrew glyphs missing from installed UI font |

Use a **sample mailbox or controlled test data**. Do not use a person's private email account or export real messages for localization screenshots.

## Automated verification

From the repository root, run:

~~~bash
cargo test -p mailcraft-ui-egui --lib
cargo fmt --all -- --check
cargo xtask ci
~~~

The i18n tests check catalog parsing, duplicate and empty entries, placeholders, trailing ellipses, coverage of interface strings and tags. They cannot establish font coverage, correct mirroring, caret behavior, accessibility or visual bidi behavior. Those require the manual checks above.

For a pull request, disclose which commands were **actually run**, their results, and the operating system. If the full CI command has not been run, do not claim it passed.

## Contribution notes

- Base wording on English UI meaning only; no third-party proprietary locale resources.
- Use consistent terminology within the same feature, but permit separate translations for a command and a folder.
- Preserve the clean-room attribution and disclose AI assistance honestly.
- Prefer a small, reproducible screenshot or minimal sample-case report for RTL issues, without real email or personal data.
- Treat reviewer-requested terminology fixes and structural RTL fixes as separate scopes when possible.

The intention is to make Hebrew catalog reviews reproducible, while keeping the boundary between **translated strings** and a **fully validated right-to-left interface** explicit.
