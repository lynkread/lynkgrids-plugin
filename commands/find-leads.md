---
description: Find leads on LinkedIn for an ideal customer and add the fits to a list in Lynkgrids
---

# Find leads

The user's request is:

$ARGUMENTS

Load the `lynkgrids-crm` skill and follow its Connect first section until the Lynkgrids
tools are present. Then:

1. Call `setup_status`. If the ideal customer is not set, ask one question and save it
   with `save_icp`; if the request above describes the audience, save that.
2. Call `find_leads`. Rank the rows against the ideal customer and the request.
3. Call `import_leads` with the picks only, each with a `fitScore` and a one-line
   `reason`, and the list the user named. If no list was named, ask for one.
4. Report how many were found and how many were added, with three or four names, and
   offer to launch a campaign on the list.

If `$ARGUMENTS` is empty, ask who the user wants to reach and which list the leads
should go into.
