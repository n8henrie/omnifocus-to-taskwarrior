# Export OmniFocus data to TaskWarrior

**WARNING:** *This project is provided as a courtesy and convenience for those
that are looking to move their data from
[OmniFocus](https://www.omnigroup.com/omnifocus) to
[TaskWarrior](https://taskwarrior.org) -- no claim is made that it will work
for you, or that your data will be converted in a manner that satisfies you,
and no unpaid support will be provided. See the [support](#support) section
below for more details.*

This script is best suited for those that aren't afraid to dig into the code
when things don't work right the first time!

### Requirements

 * Python >= 2.7.x
 * [pendulum](https://pendulum.eustace.io/)

### Usage

 1. Configure the 'Outline' section to contain those items you want exported
   * Only those items that are visible are exported, so if your Perspective is
     configured to hide items that you want exported, make the necessary
     adjustments
   * The export mode will depend on the 'Project hierarchy' setting you have
     enabled. For a flat list of actions with no associated projects, disable
     the hierarchy, for a hierarchical project list of actions, enable the
     hierarchy. See below for more information on how the hierarchy is exported
     into a format TaskWarrior can work with.

 2. Select ```File -> Export...``` from the top menu, and export the file as
    CSV.

 3. Load the CSV file to the server with TaskWarrior installed, along with the
    ```taskwarrior-export.py``` script.

 4. Make sure ```taskwarrior-export.py``` is executable then run
    ```taskwarrior-export.py -h``` to see the list of available arguments
    for the script.

 5. Run the script with your chosen arguments to generate a JSON file format
    suitable for import by TaskWarrior. If you get any errors, you're on your
    own to figure it out ;)

 6. The generated JSON file is fairly easy to inspect to see if the data is
    coming out the way you want it. The
    [TaskWarrior JSON format specification](https://taskwarrior.org/docs/design/task.html)
    can help guide you.

 7. Import the data into TaskWarrior via: ```task import [json_filename]```,
    and for heaven's sake make a backup of any existing TaskWarrior data
    beforehand!

### Supported data conversions

```
  Task ID -> Used to programmatically calculate project/subproject hierarchy.
  Type -> Not used, project/task status calculated by hierarchy
  Name -> description
  Status -> Not used, calculated from other values
  Project -> Not used, calculated from other values
  Context -> tags (optional)
  Start Date -> scheduled ('wait' optional)
  Due Date -> due
  Completion Date -> end
  Duration -> Not currently used
  Flagged -> priority (H) or 'flagged' tag (optional)
  Notes -> notes (optional)
```

#### How project hierarchies are calculated

OmniFocus and TaskWarrior have different approaches to how projects and
actions (tasks) are organized, and this requires that some adjustments
be made in the translation.

TaskWarrior cannot have projects with no tasks, nor does it have any
outlining capability for tasks (tasks can be made 'dependant' on other tasks,
but not simply live beneath other tasks in an outline fashion.

If you choose to keep a project hierarchy, here's what you need to know about
the conversion:

 * Only those items at the very deepest part of a particular hierarchy (the
   'leaves') are considered to be tasks.
 * Everything else is either a top-level project, or if it's somewhere under
   a top-level project, it is subproject via TaskWarrior's dot notation
   (eg. MyProject.MySubproject)
 * If notes are exported, any notes attached to something determined to be a
   project or subproject will have a task created under that particular project
   or subproject with a description of 'Notes'

The bottom line is that if you have really deep nesting and you use the
project hierarchy, you could end up with unusably long project names. The
rational options in this case seem to be:

 1. Flatten out your hierarchy more
 2. Use short names for the project/subprojects

### Other caveats

 * Context - Hierarchical contexts are not exported, only the final context in the hierarchy
 * Folders - Not exported, if you want to keep that hierarchy, turn it into a top-level project