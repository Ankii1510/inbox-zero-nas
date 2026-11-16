# Safety and approval

## Allowed effects

- Read inventory and metadata without approval.
- Document extraction and QA-style classification without writing files.
- State changes only after explicit user confirmation of the preview plan: create folders, rename, move, optional tag bind, digest save.

## Forbidden effects

- Delete, recycle, or batch-delete inbox files.
- Upload inbox contents to undisclosed third-party APIs outside Uliya-supported AI processing.
- Broaden an empty inbox scan to other directories.
- Guess missing absolute paths, vendors, amounts, or dates.
- Circumvent gateway or workflow approval.

## Approval expectations

Tell the user the approximate number of platform approval prompts before apply. Folder creation, rename, move, tag bind, and digest save can each require approval depending on platform policy. Wait for `waiting_approval` resume on the same delegation. Chat text such as "looks good" does not replace platform approval.

## Permissions

Operate only on folders and files the user authorized for the run. If permission is denied, stop dependent writes and report the blocked paths.
