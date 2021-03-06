# Best practices

## Xcode

### Version

The recommended version of Xcode (and Swift) for all purposes is the current available [in the App Store](https://itunes.apple.com/no/app/xcode/id497799835?mt=12).

### Project structure

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped by type and feature for greater clarity.

A recommended project structure can be found in [Project Structure](https://github.com/hyperoslo/objective-c-style-guide/blob/master/PROJECT-STRUCTURE).

### Warnings

Enable warnings by adding `-Weverything` to your Build Settings under "Other Compiler Flags". If you need to ignore a specific warning you can use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas) or add `-Wno-warning-to-be-disabled` (for example `-Wno-gnu-conditional-omitted-operand`).

### Plugin compatibility

After upgrading to a new Xcode version plugins will become disabled until their list of compatible Xcode versions gets updated. In case you can't wait for an official update by the plugins' authors you can try to run [this script](https://gist.github.com/neonichu/9487584/download#). Be sure to find out your Xcode's version UUID first and update it in the script.

You can get the Xcode's version UUID by running 
```shell
/usr/libexec/PlistBuddy -c 'Print DVTPlugInCompatibilityUUID' "$(xcode-select -p)/../Info.plist"
```

### OCLint

OCLint is a static code analysis tool for improving quality and reducing defects by inspecting C, C++ and Objective-C code [...](http://oclint.org)

### Installation

* Download the latest version of OCLint [here](http://oclint.org/downloads.html)
* Make sure that the binary is reachable in your $PATH

#### Usage

```
  $ xcodebuild clean
  $ xcodebuild >> xcodebuild.log
  $ oclint-xcodebuild
  $ oclint-json-compilation-database oclint_args "-rc LONG_LINE=110" | sed 's/\(.*\.\m\{1,2\}:[0-9]*:[0-9]*:\)/\1 warning:/' >> oclint.log
```


## Deployment

### Semantic Versioning

We support [semantic versioning](http://semver.org/), and it's important that minor releases are backwards compatible otherwise don't feel shy to make it a major release.

When making backwards compatible changes, flag your old APIs as deprecated like this:

```objc
- (NSInteger)foo:(NSInteger)bar __attribute__((deprecated("Use fooWithBar: instead")));
```


## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

**For example**

```objc
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];

    // Workaround: Selected cell only gets deselected when pressing the back button
    // dragging the screen to go back doesn't deselect the selected cell.
    // So, `self.clearsSelectionOnViewWillAppear = YES;` only works sometimes.
    NSIndexPath *selectedIndexPath = [self.tableView indexPathForSelectedRow];
    if (selectedIndexPath) {
        [self.tableView deselectRowAtIndexPath:selectedIndexPath animated:YES];
    }
}
```

## Blocks, delegates or data source

### Block
- Asynchronous (For example: networking operations)
- User inputs with multiple options (For example: UIAlertView's YES and NO)
- Data source driven inputs (For example: A table items with action blocks that were defined in the data source)
- Returns many values (For example looking for a field in a collection and returning the field and the indexPath)
- If there’s no tracked state or if state it’s defined in the same method

### Delegate
- Synchronous (For example: buttons actions in views that should perform on their parents)
- Shouldn't return values
- Provides control over performing an action (For example: UITextField's shouldEndEditing)
- User input with one action (For example: buttons actions in views that should perform on their parents)
- If tracked state is shared (if state is stored in a property or a constant)

### Data source
- Returns ONE value


## Image Naming

Image names should be named consistently to preserve organization and developer sanity. They should be named as one [lower camel case](http://c2.com/cgi/wiki?LowerCamelCase) string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `refreshBarButtonItem` / `refreshBarButtonItem@2x` and `refreshBarButtonItemSelected` / `refreshBarButtonItemSelected@2x`
* `articleNavigationBarWhite` / `articleNavigationBarWhite@2x` and `articleNavigationBarBlackSelected` / `articleNavigationBarBlackSelected@2x`.

Images should live in `Images.xcassets`.
