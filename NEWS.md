# UNISON NEWS

This file contains a summary of user-visible or important changes, in
the style of the GNU coding standards.  By user-visible, we include
changes relevant for those building unison from source.  We omit
most bugfixes and minor improvements.

As of 2022, this file (NEWS.md at top level) is used for news.

Some software has a "changelog" file that records all changes, but
unison uses git history for that, and thus there is no changelog file.

## Changes in 2.53.0

Released 2022-11-07

  * OCaml >= 4.08 is required to build unison.
  * unison can be built with (unreleased) OCaml 5.
  * Change GUI to use GTK3 (via lablgtk3) instead of GTK2 (via lablgtk2)
  * Add support for syncing extended attributes.
  * Add support for syncing ACLs.
  * On Windows, add the ability to build unison as a hybrid
    application (GUI application attached to a text console) by
    defining UI_WINOS=hybrid (see src/Makefile).  Add this to CI.
    (Doing this for non-Windows is unnecessary as all applications,
    both GUI and non-GUI, are always executed with a connection to
    stdout/stderr. GUI-only applications (ie, no stdout/stderr) is a
    Windows-only concept.)
  * Notable bugfixes
    - Merge results are stored in archive more accurately.
    - Windows `\\?\` paths now work correctly (including `\\?\Volume{GUID}\` paths).
  * CI changes
    - The macOS binaries are properly signed.
    - Add workaround for bugs in the github CI Windows builds, one of
      which resulted in the 2.52.1 GUI version failing, in the Windows
      CI build artifacts.  (This does not affect platforms other than
      Windows, and may not affect other Windows builds.)
  * Changes that should not affect behavior
    - Clean up a variety of unmaintained and unused bits, mainly
      build-related.
    - OCaml's Unix library is now extensively used also on
      Windows. This allowed removal of large amount of
      Windows-specific OCaml and mainly C code.

## Changes in 2.52.1

Released 2022-05-08

   * Deprecate ocaml < 4.08: 2.53.0 will require 4.08 or higher
   * Add man page
   * Remove rsh:// URI scheme (ssh:// of course remains).
   * Significant bugfixes and minor improvements
   * Deprecate "backups" preference (see manual for alternatives)
   * Deprecate "stream" and "halfduplex" preferences

## Changes in 2.52.0

Released 2022-03-12

   * Feature negotiation, compatible with 2.51.
   * New archive format (independent of ocaml version, based on umarshal)
     Upgrade is automatic.
   * New wire protocol (independent of ocaml version, based on umarshal)
     New protocol is used if both sides are >= 2.52.0.
   * Compatibility with 2.48
   * Support for unix-domain sockets
   * Many bugfixes and minor improvements
   * ocaml compatibility is now >= 4.01
   * NEWS is now in NEWS.md and not in the manual

## Changes in 2.51.5

Released 2021-12-18

     * Restore OCaml compat to before 4.02
     * dune/opam improvements/fixes
     * Improve GTK UI by using GtkTreeView
     * Add support for syncing symlinks on Windows (NTFS)
     * Improve ssh support on Windows (hide Windows console in GUI mode)
     * Many bugfixes and minor improvements

## Changes in 2.51.4

Released 2021-06-24

     * OCaml 4.12 support
     * fsmonitor improvements and Solaris support
     * Color support in text UI, with a new preference, disabled by
       NO_COLOR.
     * Interactive profile selection in text UI, enabled by a new
       preference.
     * Working files are stored in the unison directory (typically
       /.unison) rather than $HOME.
     * Build cleanups, CI improvements, housekeeping
     * Many bugfixes and minor improvements

## Changes in 2.51.3

Released 2020-10-21

     * Some nontrivial changes to profile parsing (G.raud Meyer)
          + '=' has been considered whitespace until now: several
            following chars are considered as only one; trailing chars are
            discarded; any non empty sequence of char is splitting. This
            is non standard and leads to confusion, for example -ignore==
            'Name .*=*' is valid when -ignore='Name .*=*' is not, and
            worse -ignore='Name *=' is the same as -ignore='Name *'. The
            parser now takes just a single '=' as delimiter after the
            option name. Other = characters are considered as part of the
            value being assigned to the option.
     * Numerous improvements to the text user-interface (G.raud Meyer)
          + New key-commands that restrict the display to a set of
            "matching" items: ones that are offering to propagate changes
            in a particular direction, conflicts, files to be merged,
            etc., plus several more useful key-commands. Type "?" to
            Unison to see all available commands.

## Changes in 2.51.0 through 2.51.2

     * Repository transplanted from SVN to Git and moved to GitHub ()
       (https://github.com/bcpierce00/unison).
     * Add a new preference, 'atomic', for specifying directories that
       should be treated atomically: if there are changes within such a
       directory in both replicase, the whole directory is marked as a
       conflict instead of propagating any of the changes. Thanks to
       Julian Squires for submitting this patch!
     * OSX / macOS
          + Ported to 10.13, High Sierra, and Apple's new APFS (earlier
            versions of Unison break because of new behavior of
            AppleDouble files)
          + Replaced Growl with OS X native notification center.
     * Miscellaneous:
          + The OCaml compiler version is now included in the "connection
            header -- the string that's printed when connecting to a
            remote server -- to facilitate debugging version mismatch
            issues.
          + Compatible with OCaml 4.06.
          + Added a DockerFile for the convenience of Docker users.
          + Many small bugfixes and UI improvements.

## Changes in 2.48

     * Incorporated a patch from Christopher Zimmermann to replace the
       Uprintf module (which doesn't work with OCaml 4.02, causing Unison
       to crash) with equivalent functionality from the standard library.
     * Incorporated a refresh of the OSX GUI, contributed by Alan Shutko.
     * Added a maxsizethreshold option, which prevents the transfer of
       files larger than the size specified (in Kb).
     * Added a "copyonconflict" preference, to make a copy of files that
       would otherwise be overwritten or deleted in case of conflicting
       changes. (This makes it possible to automatically resolve conflicts
       in a fairly safe way when synchronizing continuously, in
       combination with the "repeat = watch" and "prefer = newer"
       preferences.
     * File system monitoring:
          + The file watcher now fails when unable to watch a directory,
            rather than silently ignoring the issue.
          + File system monitoring: more robust communication with the
            helper program (in socket mode, the unison server will still
            work properly despite unexpected unison client
            disconnections).
          + A bytecode version of unison-fsmonitor is now produced by
            "make NATIVE=false"
          + Improved search for unison-fsmonitor
          + Detect when the helper process exits.
          + More robust file watching helper programs for Windows and
            Linux. They communicate with Unison through pipes (Unison
            redirects stdin and stdout), using a race-free protocol.
          + Retries paths with failures using an exponential backoff
            algorithm.
          + The information returned by the file watchers are used
            independently for each replica; thus, when only one replica
            has changes, Unison will only rescan this replica.
          + When available, used by the graphical UIs to speed up
            rescanning (can be disabled by setting the new watch
            preference to
          + Small fix to the way fsmonitor.py gets invoked when using the
            file watching functionality, suggested by Josh Berdine. Unison
            will now look for fsmonitor.py in the same directory where the
            Unison executable itself lives.
     * Minor:
          + Fixed a bug in export procedure that was messing up
            documentation strings.
          + Incorporated a patch from Irányossy Knoblauch Artúr to make
            temp file names fit within 143 characters (to make eCryptFS
            happy).
          + Added a string to the Conflict direction to document the
            reason of the conflict.
          + Log conflicts and problems in the text UI even if nothing is
            propagated.
          + Use hash function from OCaml 3.x for comparing archives, even
            when compiled with OCaml 4.x.
          + Do not restart Unison in case of uncaught exception when the
            repeat preference is set. This seems safer. And it does not
            work, for instance, in case of lost connection.
          + Fix Unix.readlink invalid argument error under Windows
          + Fix a crash when the output of the diff program is too large.
          + Fixed Makefile for cross-compiling towards Windows (updated to
            MinGW-w64)

## Changes in very old versions

(Note that these are written 'Chagnes since' and thus the content
applies to the release after that.)

   Changes since 2.40.63:
     * New preference fastercheckUNSAFE, which can be used (with care!) to
       achieve much faster update detection when all the common files in
       the two replicas are known to be identical. See the manual for more
       information.
       This feature should still be considered experimental, but it's
       ready for other people to try out.
     * Added option clientHostName. If specified, it will be used to as
       the client host name, overriding UNISONLOCALHOSTNAME and the actual
       host name.
     * OS X GUI:
          + fix crash under Lion, because of problems with the toolbar,
            using the fix suggested in
            http://blitzbasic.com/Community/posts.php?topic=95778.
          + uimacnew09 is now the standard graphical interface on OSX
          + A small improvement to the uimacnew09 interface from Alan
            Schmitt and Steve Kalkwarf: when Unison is run with the -batch
            flag, the interface will now automatically propagate changes
            and terminate, without waiting for user interaction.
          + Show a modal warning window if there is no archive for the
            hosts. The user can then choose to exit or proceed (proceed is
            the default). The window is not shown if the batch preference
            is true.
          + file details panel selectable
     * GTK GUI:
          + New version of uigtk2.ml from Matt Zagrabelny that reorganizes
            the icons in a slightly more intuitive way.
     * Minor fixes:
          + Setting the prefer preference to older or newer now propagates
            deletions when there is no conflict.
          + Correctly quote the path when running merge commands.
          + Add quotes to paths when calling external file watcher
            utility.
          + Incorporate a patch to fsmonitor.py (the external filewatcher
            utility) from Tomasz Zernicki to make it work better under
            Windows.
          + Incorporated new version of fsmonitor.py from Christophe Gohle
          + Fixed incompatibility with OpenSSH 5.6.
          + Fixed fingerprint cache: do not cache file properties
          + Some spelling corrections in documentation and comments from
            Stephane Glondu
          + Fixed O_APPEND mode for open under Windows
          + Fixed String.sub invalid argument error when an AppleDouble
            file does not contain a finder information field
          + Trim duplicate paths when using "-repeat watch"
          + Unison now passes path arguments and -follow directives to
            fsmonitor.py. This seems to work except for one small issue
            with how fsmonitor.py treats -follow directives for
            directories that don't exist (or maybe this is an issue with
            how it treats any kind of monitoring when the thing being
            monitored doesn't exist?). If we create a symlink to a
            nonexistent directory, give Unison (hence fsmonitor.py) a
            'follow' directive for the symlink, start unison, and then
            create the directory, fsmonitor.py misses the change.
          + Lines added in profile files by unison always start at a new
            line

   Changes since 2.40.1:
     * Added "BelowPath" patterns, that match a path as well as all paths
       below (convenient to use with nodeletion,update,creationpartial
       preferences)
     * Added a "fat" preference that makes Unison use the right options
       when one of the replica is on a FAT filesystem.
     * Allow "prefer/force=newer" even when not synchronizing modification
       times. (The reconciler will not be aware of the modification time
       of unchanged files, so the synchronization choices of Unison can be
       different from when "times=true", but the behavior remains sane:
       changed files with the most recent modification time will be
       propagated.)
     * Minor fixes and improvements:
          + Compare filenames up to decomposition in case sensitive mode
            when one host is running MacOSX and the unicode preference is
            set to true.
          + Rsync: somewhat faster compressor
          + Make Unicode the default on all architectures (it was only the
            default when a Mac OS X or Windows machine was involved).

   Changes since 2.32:
     * Major enhancement: Unicode support.
          + Unison should now handle unicode filenames correctly on all
            platforms.
          + This functionality is controlled by a new preference unicode.
          + Unicode mode is now the default when one of the hosts is under
            Windows or MacOS. This may make upgrades a bit more painful
            (the archives cannot be reused), but this is a much saner
            default.
     * Partial transfer of directories. If an error occurs while
       transferring a directory, the part transferred so far is copied
       into place (and the archives are updated accordingly). The
       "maxerrors" preference controls how many transfer error Unison will
       accept before stopping the transfer of a directory (by default,
       only one). This makes it possible to transfer most of a directory
       even if there are some errors. Currently, only the first error is
       reported by the GUIs.
       Also, allow partial transfer of a directory when there was an error
       deep inside this directory during update detection. At the moment,
       this is only activated with the text and GTK UIs, which have been
       modified so that they show that the transfer is going to be partial
       and so that they can display all errors.
     * Improvement to the code for resuming directory transfers:
          + if a file was not correctly transferred (or the source has
            been modified since, with unchanged size), Unison performs a
            new transfer rather than failing
          + spurious files are deleted (this can happen if a file is
            deleted on the source replica before resuming the transfer;
            not deleting the file would result in it reappearing on the
            target replica)
     * Experimental streaming protocol for transferring file contents (can
       be disabled by setting the directive "stream" to false): file
       contents is transferred asynchronously (without waiting for a
       response from the destination after each chunk sent) rather than
       using the synchronous RPC mechanism. As a consequence:
          + Unison now transfers the contents of a single file at a time
            (Unison used to transfer several contents simultaneously in
            order to hide the connection latency.)
          + the transfer of large files uses the full available bandwidth
            and is not slowed done due to the connection latency anymore
          + we get performance improvement for small files as well by
            scheduling many files simultaneously (as scheduling a file for
            transfer consume little resource: it does not mean allocating
            a large buffer anymore)
     * Changes to the internal implementation of the rsync algorithm:
          + use longer blocks for large files (the size of a block is the
            square root of the size of the file for large files);
          + transmit less checksum information per block (we still have
            less than one chance in a hundred million of transferring a
            file incorrectly, and Unison will catch any transfer error
            when fingerprinting the whole file)
          + avoid transfer overhead (which was 4 bytes per block)
       For a 1G file, the first optimization saves a factor 50 on the
       amount of data transferred from the target to the source (blocks
       are 32768 bytes rather than just 700 bytes). The two other
       optimizations save another factor of 2 (from 24 bytes per block
       down to 10).
     * Implemented an on-disk file fingerprint cache to speed-up update
       detection after a crash: this way, Unison does not have do
       recompute all the file fingerprints from scratch.
          + When Unison detects that the archive case-sensitivity mode
            does not match the current settings, it populates the
            fingerprint cache using the archive contents. This way,
            changing the case-sensitivity mode should be reasonably fast.
     * New preferences "noupdate=root", "nodeletion=root",
       "nocreation=root" that prevent Unison from performing files
       updates, deletions or creations on the given root. Also 'partial'
       versions of 'noupdate', 'nodeletion' and 'nocreation'
     * Limit the number of simultaneous external copy program ("copymax"
       preference)
     * New "links" preference. When set to false, Unison will report an
       error on symlinks during update detection. (This is the default
       when one host is running Windows but not Cygwin.) This is better
       than failing during propagation.
     * Added a preference "halfduplex" to force half-duplex communication
       with the server. This may be useful on unreliable links (as a more
       efficient alternative to "maxthreads = 1").
     * Renamed preference "pretendwin" to "ignoreinodenumbers" (an alias
       is kept for backwards compatibility).
     * Ignore one-second differences when synchronizing modification time.
       (Technically, this is an incompatible archive format change, but it
       is backward compatible. To trigger a problem, a user would have to
       synchronize modification times on a filesystem with a two-second
       granularity and then downgrade to a previous version of Unison,
       which does not work well in such a case. Thus, it does not seem
       worthwhile to increment the archive format number, which would
       impact all users.)
     * Do not keep many files simultaneously opened anymore when the rsync
       algorithm is in use.
     * Add "ignorearchives" preference to ignore existing archives (to
       avoid forcing users to delete them manually, in situations where
       one archive has gotten deleted or corrupted).
     * Mac OS
          + fixed rsync bug which could result in an "index out of bounds"
            error when transferring resource forks.
          + Fixed bug which made Unison ignore finder information and
            resource fork when compiled to 64bit on Mac OSX.
          + should now be 64 bit clean (the Growl framework is not up to
            date, though)
          + Made the bridge between Objective C and Ocaml code GC friendly
            (it was allocating ML values and putting them in an array
            which was not registered with the GC)
          + use darker grey arrows (patch contributed by Eric Y. Kow)
     * GTK user interface
          + assistant for creating profiles
          + profile editor
          + pop up a summary window when the replicas are not fully
            synchronized after transport
          + display estimated remaining time and transfer rate on the
            progress bar
          + allow simultaneous selection of several items
          + Do not reload the preference file before a new update
            detection if it is unchanged
          + disabled scrolling to the first unfinished item during
            transport. It goes way too fast when lot of small files are
            synchronized, and it makes it impossible to browse the file
            list during transport.
          + take into account the "height" preference again
          + the internal list of selected reconciler item was not always
            in sync with what was displayed (GTK bug?); workaround
            implemented
          + Do not display "Looking for change" messages during
            propagation (when checking the targe is unchanged) but only
            during update detection
          + Apply patch to fix some crashes in the OSX GUI, thanks to Onne
            Gorter.
     * Text UI
          + During update detection, display status by updating a single
            line rather than generating a new line of output every so
            often. Should be less confusing.
     * Windows
          + Fastcheck is now the default under Windows. People mostly use
            NTFS nowadays and the Unicode API provides an equivalent to
            inode numbers for this filesystem.
          + Only use long UNC path for accessing replicas (as '..' is not
            handled with this format of paths, but can be useful)
          + Windows text UI: now put the console into UTF-8 output mode.
            This is the right thing to do when in Unicode mode, and is no
            worse than what we had previously otherwise (the console use
            some esoteric encoding by default). This only works when using
            a Unicode font instead of the default raster font.
          + Don't get the home directory from environment variable HOME
            under Windows (except for Cygwin binaries): we don't want the
            behavior of Unison to depends on whether it is run from a
            Cygwin shell (where HOME is set) or in any other way (where
            HOME is usually not set).
     * Miscellaneous fixes and improvements
          + Made a server waiting on a socket more resilient to unexpected
            lost connections from the client.
          + Small patch to property setting code suggested by Ulrich
            Gernkow.
          + Several fixes to the change transfer functions (both the
            internal ones and external transfers using rsync). In
            particular, limit the number of simultaneous transfer using an
            rsync (as the rsync algorithm can use a large amount of memory
            when processing huge files)
          + Keep track of which file contents are being transferred, and
            delay the transfer of a file when another file with the same
            contents is currently being transferred. This way, the second
            transfer can be skipped and replaced by a local copy.
          + Experimental update detection optimization: do not read the
            contents of unchanged directories
          + When a file transfer fails, turn off fastcheck for this file
            on the next sync.
          + Fixed bug with case insensitive mode on a case sensitive
            filesystem:
               o if file "a/a" is created on one replica and directory "A"
                 is created on the other, the file failed to be
                 synchronized the first time Unison is run afterwards, as
                 Unison uses the wrong path "a/a" (if Unison is run again,
                 the directories are in the archive, so the right path is
                 used);
               o if file "a" appears on one replica and file "A" appears
                 on the other with different contents, Unison was unable
                 to synchronize them.
          + Improved error reporting when the destination is updated
            during synchronization: Unison now tells which file has been
            updated, and how.
          + Limit the length of temporary file names
          + Case sensitivity information put in the archive (in a backward
            compatible way) and checked when the archive is loaded
          + Got rid of the 16mb marshalling limit by marshalling to a
            bigarray.
          + Resume copy of partially transferred files.

   Changes since 2.31:
     * Small user interface changes
          + Small change to text UI "scanning..." messages, to print just
            directories (hopefully making it clearer that individual files
            are not necessarily being fingerprinted).
     * Minor fixes and improvements:
          + Ignore one hour differences when deciding whether a file may
            have been updated. This avoids slow update detection after
            daylight saving time changes under Windows. This makes Unison
            slightly more likely to miss an update, but it should be safe
            enough.
          + Fix a small bug that was affecting mainly windows users. We
            need to commit the archives at the end of the sync even if
            there are no updates to propagate because some files (in fact,
            if we've just switched to DST on windows, a LOT of files)
            might have new modtimes in the archive. (Changed the text UI
            only. It's less clear where to change the GUI.)
          + Don't delete the temp file when a transfer fails due to a
            fingerprint mismatch (so that we can have a look and see why!)
            We've also added more debugging code togive more informative
            error messages when we encounter the dreaded and longstanding
            "assert failed during file transfer" bug
          + Incorrect paths ("path" directive) now result in an error
            update item rather than a fatal error.
          + Create parent directories (with correct permissions) during
            transport for paths which point to non-existent locations in
            the destination replica.

   Changes since 2.27:
     * If Unison is interrupted during a directory transfer, it will now
       leave the partially transferred directory intact in a temporary
       location. (This maintains the invariant that new files/directories
       are transferred either completely or not at all.) The next time
       Unison is run, it will continue filling in this temporary
       directory, skipping transferring files that it finds are already
       there.
     * We've added experimental support for invoking an external file
       transfer tool for whole-file copies instead of Unison's built-in
       transfer protocol. Three new preferences have been added:
          + copyprog is a string giving the name (and command-line
            switches, if needed) of an external program that can be used
            to copy large files efficiently. By default, rsync is invoked,
            but other tools such as scp can be used instead by changing
            the value of this preference. (Although this is not its
            primary purpose, rsync is actually a pretty fast way of
            copying files that don't already exist on the receiving host.)
            For files that do already exist on (but that have been changed
            in one replica), Unison will always use its built-in
            implementation of the rsync algorithm.
          + Added a "copyprogrest" preference, so that we can give
            different command lines for invoking the external copy utility
            depending on whether a partially transferred file already
            exists or not. (Rsync doesn't seem to care about this, but
            other utilities may.)
          + copythreshold is an integer (-1 by default), indicating above
            what filesize (in megabytes) Unison should use the external
            copying utility specified by copyprog. Specifying 0 will cause
            ALL copies to use the external program; a negative number will
            prevent any files from using it. (Default is -1.)
       Thanks to Alan Schmitt for a huge amount of hacking and to an
       anonymous sponsor for suggesting and underwriting this extension.
     * Small improvements:
          + Added a new preference, dontchmod. By default, Unison uses the
            chmod system call to set the permission bits of files after it
            has copied them. But in some circumstances (and under some
            operating systems), the chmod call always fails. Setting this
            preference completely prevents Unison from ever calling chmod.
          + Don't ignore files that look like backup files if the
            backuplocation preference is set to central
          + Shortened the names of several preferences. The old names are
            also still supported, for backwards compatibility, but they do
            not appear in the documentation.
          + Lots of little documentation tidying. (In particular,
            preferences are separated into Basic and Advanced! This should
            hopefully make Unison a little more approachable for new
            users.
          + Unison can sometimes fail to transfer a file, giving the
            unhelpful message "Destination updated during synchronization"
            even though the file has not been changed. This can be caused
            by programs that change either the file's contents or the
            file's extended attributes without changing its modification
            time. It's not clear what is the best fix for this - it is not
            Unison's fault, but it makes Unison's behavior puzzling - but
            at least Unison can be more helpful about suggesting a
            workaround (running once with fastcheck set to false). The
            failure message has been changed to give this advice.
          + Further improvements to the OS X GUI (thanks to Alan Schmitt
            and Craig Federighi).
     * Very preliminary support for triggering Unison from an external
       filesystem-watching utility. The current implementation is very
       simple, not efficient, and almost completely untested--not ready
       for real users. But if someone wants to help improve it (e.g., by
       writing a filesystem watcher for your favorite OS), please make
       yourself known!
       On the Unison side, the new behavior is very simple:
          + use the text UI
          + start Unison with the command-line flag "-repeat FOO", where
            FOO is name of a file where Unison should look for
            notifications of changes
          + when it starts up, Unison will read the whole contents of this
            file (on both hosts), which should be a newline-separated list
            of paths (relative to the root of the synchronization) and
            synchronize just these paths, as if it had been started with
            the "-path=xxx" option for each one of them
          + when it finishes, it will sleep for a few seconds and then
            examine the watchfile again; if anything has been added, it
            will read the new paths, synchronize them, and go back to
            sleep
          + that's it!
       To use this to drive Unison "incrementally," just start it in this
       mode and start up a tool (on each host) to watch for new changes to
       the filesystem and append the appropriate paths to the watchfile.
       Hopefully such tools should not be too hard to write.
     * Bug fixes:
          + Fixed a bug that was causing new files to be created with
            permissions 0x600 instead of using a reasonable default (like
            0x644), if the 'perms' flag was set to 0. (Bug reported by Ben
            Crowell.)
          + Follow maxthreads preference when transferring directories.

   Changes since 2.17:
     * Major rewrite and cleanup of the whole Mac OS X graphical user
       interface by Craig Federighi. Thanks, Craig!!!
     * Small fix to ctime (non-)handling in update detection under windows
       with fastcheck.
     * Several small fixes to the GTK2 UI to make it work better under
       Windows [thanks to Karl M for these].
     * The backup functionality has been completely rewritten. The
       external interface has not changed, but numerous bugs, irregular
       behaviors, and cross-platform inconsistencies have been corrected.
     * The Unison project now accepts donations via PayPal. If you'd like
       to donate, you can find a link to the donation page on the Unison
       home page (http://www.cis.upenn.edu/ bcpierce/unison/lists.html).
     * Some important safety improvements:
          + Added a new mountpoint preference, which can be used to
            specify a path that must exist in both replicas at the end of
            update detection (otherwise Unison aborts). This can be used
            to avoid potentially dangerous situations when Unison is used
            with removable media such as external hard drives and compact
            flash cards.
          + The confirmation of "big deletes" is now controlled by a
            boolean preference confirmbigdeletes. Default is true, which
            gives the same behavior as previously. (This functionality is
            at least partly superseded by the mountpoint preference, but
            it has been left in place in case it is useful to some
            people.)
          + If Unison is asked to "follow" a symbolic link but there is
            nothing at the other end of the link, it will now flag this
            path as an error, rather than treating the symlink itself as
            missing or deleted. This avoids a potentially dangerous
            situation where a followed symlink points to an external
            filesystem that might be offline when Unison is run (whereupon
            Unison would cheerfully delete the corresponding files in the
            other replica!).
     * Smaller changes:
          + Added forcepartial and preferpartial preferences, which behave
            like force and prefer but can be specified on a per-path
            basis. [Thanks to Alan Schmitt for this.]
          + A bare-bones self test feature was added, which runs unison
            through some of its paces and checks that the results are as
            expected. The coverage of the tests is still very limited, but
            the facility has already been very useful in debugging the new
            backup functionality (especially in exposing some subtle
            cross-platform issues).
          + Refined debugging code so that the verbosity of individual
            modules can be controlled separately. Instead of just putting
            '-debug verbose' on the command line, you can put '-debug
            update+', which causes all the extra messages in the Update
            module, but not other modules, to be printed. Putting '-debug
            verbose' causes all modules to print with maximum verbosity.
          + Removed mergebatch preference. (It never seemed very useful,
            and its semantics were confusing.)
          + Rewrote some of the merging functionality, for better
            cooperation with external Harmony instances.
          + Changed the temp file prefix from .# to .unison.
          + Compressed the output from the text user interface
            (particularly when run with the -terse flag) to make it easier
            to interpret the results when Unison is run several times in
            succession from a script.
          + Diff and merge functions now work under Windows.
          + Changed the order of arguments to the default diff command (so
            that the + and - annotations in diff's output are reversed).
          + Added .mpp files to the "never fastcheck" list (like .xls
            files).
     * Many small bugfixes, including:
          + Fixed a longstanding bug regarding fastcheck and daylight
            saving time under Windows when Unison is set up to synchronize
            modification times. (Modification times cannot be updated in
            the archive in this case, so we have to ignore one hour
            differences.)
          + Fixed a bug that would occasionally cause the archives to be
            left in non-identical states on the two hosts after
            synchronization.
          + Fixed a bug that prevented Unison from communicating correctly
            between 32- and 64-bit architectures.
          + On windows, file creation times are no longer used as a proxy
            for inode numbers. (This is unfortunate, as it makes fastcheck
            a little less safe. But it turns out that file creation times
            are not reliable under Windows: if a file is removed and a new
            file is created in its place, the new one will sometimes be
            given the same creation date as the old one!)
          + Set read-only file to R/W on OSX before attempting to change
            other attributes.
          + Fixed bug resulting in spurious "Aborted" errors during
            transport (thanks to Jerome Vouillon)
          + Enable diff if file contents have changed in one replica, but
            only properties in the other.
          + Removed misleading documentation for 'repeat' preference.
          + Fixed a bug in merging code where Unison could sometimes
            deadlock with the external merge program, if the latter
            produced large amounts of output.
          + Workaround for a bug compiling gtk2 user interface against
            current versions of gtk2+ libraries.
          + Added a better error message for "ambiguous paths".
          + Squashed a longstanding bug that would cause file transfer to
            fail with the message "Failed: Error in readWrite: Is a
            directory."
          + Replaced symlinks with copies of their targets in the Growl
            framework in src/uimac. This should make the sources easier to
            check out from the svn repository on WinXP systems.
          + Added a workaround (suggested by Karl M.) for the problem
            discussed on the unison users mailing list where, on the
            Windows platform, the server would hang when transferring
            files. I conjecture that the problem has to do with the RPC
            mechanism, which was used to make a call back from the server
            to the client (inside the Trace.log function) so that the log
            message would be appended to the log file on the client. The
            workaround is to dump these messages (about when xferbycopying
            shortcuts are applied and whether they succeed) just to the
            standard output of the Unison process, not to the log file.

   Changes since 2.13.0:
     * The features for performing backups and for invoking external merge
       programs have been completely rewritten by Stephane Lescuyer
       (thanks, Stephane!). The user-visible functionality should not
       change, but the internals have been rationalized and there are a
       number of new features. See the manual (in particular, the
       description of the backupXXX preferences) for details.
     * Incorporated patches for ipv6 support, contributed by Samuel
       Thibault. (Note that, due to a bug in the released OCaml 3.08.3
       compiler, this code will not actually work with ipv6 unless
       compiled with the CVS version of the OCaml compiler, where the bug
       has been fixed; however, ipv4 should continue to work normally.)
     * OSX interface:
          + Incorporated Ben Willmore's cool new icon for the Mac UI.
     * Small fixes:
          + Fixed off by one error in month numbers (in printed dates)
            reported by Bob Burger

   Changes since 2.12.0:
     * New convention for release numbering: Releases will continue to be
       given numbers of the form X.Y.Z, but, from now on, just the major
       version number (X.Y) will be considered significant when checking
       compatibility between client and server versions. The third
       component of the version number will be used only to identify
       "patch levels" of releases.
       This change goes hand in hand with a change to the procedure for
       making new releases. Candidate releases will initially be given
       "beta release" status when they are announced for public
       consumption. Any bugs that are discovered will be fixed in a
       separate branch of the source repository (without changing the
       major version number) and new tarballs re-released as needed. When
       this process converges, the patched beta version will be dubbed
       stable.
     * Warning (failure in batch mode) when one path is completely
       emptied. This prevents Unison from deleting everything on one
       replica when the other disappear.
     * Fix diff bug (where no difference is shown the first time the diff
       command is given).
     * User interface changes:
          + Improved workaround for button focus problem (GTK2 UI)
          + Put leading zeroes in date fields
          + More robust handling of character encodings in GTK2 UI
          + Changed format of modification time displays, from modified at
            hh:mm:ss on dd MMM, yyyy to modified on yyyy-mm-dd hh:mm:ss
          + Changed time display to include seconds (so that people on FAT
            filesystems will not be confused when Unison tries to update a
            file time to an odd number of seconds and the filesystem
            truncates it to an even number!)
          + Use the diff "-u" option by default when showing differences
            between files (the output is more readable)
          + In text mode, pipe the diff output to a pager if the
            environment variable PAGER is set
          + Bug fixes and cleanups in ssh password prompting. Now works
            with the GTK2 UI under Linux. (Hopefully the Mac OS X one is
            not broken!)
          + Include profile name in the GTK2 window name
          + Added bindings ',' (same as '<') and '.' (same as '>') in the
            GTK2 UI
     * Mac GUI:
          + actions like < and > scroll to the next item as necessary.
          + Restart has a menu item and keyboard shortcut (command-R).
          + Added a command-line tool for Mac OS X. It can be installed
            from the Unison menu.
          + New icon.
          + Handle the "help" command-line argument properly.
          + Handle profiles given on the command line properly.
          + When a profile has been selected, the profile dialog is
            replaced by a "connecting" message while the connection is
            being made. This gives better feedback.
          + Size of left and right columns is now large enough so that
            "PropsChanged" is not cut off.
     * Minor changes:
          + Disable multi-threading when both roots are local
          + Improved error handling code. In particular, make sure all
            files are closed in case of a transient failure
          + Under Windows, use $UNISON for home directory as a last resort
            (it was wrongly moved before $HOME and $USERPROFILE in Unison
            2.12.0)
          + Reopen the logfile if its name changes (profile change)
          + Double-check that permissions and modification times have been
            properly set: there are some combination of OS and filesystem
            on which setting them can fail in a silent way.
          + Check for bad Windows filenames for pure Windows
            synchronization also (not just cross architecture
            synchronization). This way, filenames containing backslashes,
            which are not correctly handled by unison, are rejected right
            away.
          + Attempt to resolve issues with synchronizing modification
            times of read-only files under Windows
          + Ignore chmod failures when deleting files
          + Ignore trailing dots in filenames in case insensitive mode
          + Proper quoting of paths, files and extensions ignored using
            the UI
          + The strings CURRENT1 and CURRENT2 are now correctly substitued
            when they occur in the diff preference
          + Improvements to syncing resource forks between Macs via a
            non-Mac system.

   Changes since 2.10.2:
     * INCOMPATIBLE CHANGE: Archive format has changed.
     * Source code availability: The Unison sources are now managed using
       Subversion. One nice side-effect is that anonymous checkout is now
       possible, like this:
        svn co https://cvs.cis.upenn.edu:3690/svnroot/unison/

       We will also continue to export a "developer tarball" of the
       current (modulo one day) sources in the web export directory. To
       receive commit logs for changes to the sources, subscribe to the
       unison-hackers list (http://www.cis.upenn.edu/
       bcpierce/unison/lists.html).
     * Text user interface:
          + Substantial reworking of the internal logic of the text UI to
            make it a bit easier to modify.
          + The dumbtty flag in the text UI is automatically set to true
            if the client is running on a Unix system and the EMACS
            environment variable is set to anything other than the empty
            string.
     * Native OS X gui:
          + Added a synchronize menu item with keyboard shortcut
          + Added a merge menu item, still needs to be debugged
          + Fixes to compile for Panther
          + Miscellaneous improvements and bugfixes
     * Small changes:
          + Changed the filename checking code to apply to Windows only,
            instead of OS X as well.
          + Finder flags now synchronized
          + Fallback in copy.ml for filesystem that do not support O_EXCL
          + Changed buffer size for local file copy (was highly
            inefficient with synchronous writes)
          + Ignore chmod failure when deleting a directory
          + Fixed assertion failure when resolving a conflict content
            change / permission changes in favor of the content change.
          + Workaround for transferring large files using rsync.
          + Use buffered I/O for files (this is the only way to open files
            in binary mode under Cygwin).
          + On non-Cygwin Windows systems, the UNISON environment variable
            is now checked first to determine where to look for Unison's
            archive and preference files, followed by HOME and USERPROFILE
            in that order. On Unix and Cygwin systems, HOME is used.
          + Generalized diff preference so that it can be given either as
            just the command name to be used for calculating diffs or else
            a whole command line, containing the strings CURRENT1 and
            CURRENT2, which will be replaced by the names of the files to
            be diff'ed before the command is called.
          + Recognize password prompts in some newer versions of ssh.

   Changes since 2.9.20:
     * INCOMPATIBLE CHANGE: Archive format has changed.
     * Major functionality changes:
          + Major tidying and enhancement of 'merge' functionality. The
            main user-visible change is that the external merge program
            may either write the merged output to a single new file, as
            before, or it may modify one or both of its input files, or it
            may write two new files. In the latter cases, its
            modifications will be copied back into place on both the local
            and the remote host, and (if the two files are now equal) the
            archive will be updated appropriately. More information can be
            found in the user manual. Thanks to Malo Denielou and Alan
            Schmitt for these improvements.
            Warning: the new merging functionality is not completely
            compatible with old versions! Check the manual for details.
          + Files larger than 2Gb are now supported.
          + Added preliminary (and still somewhat experimental) support
            for the Apple OS X operating system.
               o Resource forks should be transferred correctly. (See the
                 manual for details of how this works when synchronizing
                 HFS with non-HFS volumes.) Synchronization of file type
                 and creator information is also supported.
               o On OSX systems, the name of the directory for storing
                 Unison's archives, preference files, etc., is now
                 determined as follows:
                    # if ~/.unison exists, use it
                    # otherwise, use ~/Library/Application Support/Unison,
                      creating it if necessary.
               o A preliminary native-Cocoa user interface is under
                 construction. This still needs some work, and some users
                 experience unpredictable crashes, so it is only for
                 hackers for now. Run make with UISTYLE=mac to build this
                 interface.
     * Minor functionality changes:
          + Added an ignorelocks preference, which forces Unison to
            override left-over archive locks. (Setting this preference is
            dangerous! Use it only if you are positive you know what you
            are doing.)
          + Added a new preference assumeContentsAreImmutable. If a
            directory matches one of the patterns set in this preference,
            then update detection is skipped for files in this directory.
            (The purpose is to speed update detection for cases like Mail
            folders, which contain lots and lots of immutable files.) Also
            a preference assumeContentsAreImmutableNot, which overrides
            the first, similarly to ignorenot. (Later amendment: these
            preferences are now called immutable and immutablenot.)
          + The ignorecase flag has been changed from a boolean to a
            three-valued preference. The default setting, called default,
            checks the operating systems running on the client and server
            and ignores filename case if either of them is OSX or Windows.
            Setting ignorecase to true or false overrides this behavior.
            If you have been setting ignorecase on the command line using
            -ignorecase=true or -ignorecase=false, you will need to change
            to -ignorecase true or -ignorecase false.
          + a new preference, 'repeat', for the text user interface
            (only). If 'repeat' is set to a number, then, after it
            finishes synchronizing, Unison will wait for that many seconds
            and then start over, continuing this way until it is killed
            from outside. Setting repeat to true will automatically set
            the batch preference to true.
          + Excel files are now handled specially, so that the fastcheck
            optimization is skipped even if the fastcheck flag is set.
            (Excel does some naughty things with modtimes, making this
            optimization unreliable and leading to failures during change
            propagation.)
          + The ignorecase flag has been changed from a boolean to a
            three-valued preference. The default setting, called
            'default', checks the operating systems running on the client
            and server and ignores filename case if either of them is OSX
            or Windows. Setting ignorecase to 'true' or 'false' overrides
            this behavior.
          + Added a new preference, 'repeat', for the text user interface
            (only, at the moment). If 'repeat' is set to a number, then,
            after it finishes synchronizing, Unison will wait for that
            many seconds and then start over, continuing this way until it
            is killed from outside. Setting repeat to true will
            automatically set the batch preference to true.
          + The 'rshargs' preference has been split into 'rshargs' and
            'sshargs' (mainly to make the documentation clearer). In fact,
            'rshargs' is no longer mentioned in the documentation at all,
            since pretty much everybody uses ssh now anyway.
     * Documentation
          + The web pages have been completely redesigned and reorganized.
            (Thanks to Alan Schmitt for help with this.)
     * User interface improvements
          + Added a GTK2 user interface, capable (among other things) of
            displaying filenames in any locale encoding. Kudos to Stephen
            Tse for contributing this code!
          + The text UI now prints a list of failed and skipped transfers
            at the end of synchronization.
          + Restarting update detection from the graphical UI will reload
            the current profile (which in particular will reset the -path
            preference, in case it has been narrowed by using the "Recheck
            unsynchronized items" command).
          + Several small improvements to the text user interface,
            including a progress display.
     * Bug fixes (too numerous to count, actually, but here are some):
          + The maxthreads preference works now.
          + Fixed bug where warning message about uname returning an
            unrecognized result was preventing connection to server. (The
            warning is no longer printed, and all systems where 'uname'
            returns anything other than 'Darwin' are assumed not to be
            running OS X.)
          + Fixed a problem on OS X that caused some valid file names
            (e.g., those including colons) to be considered invalid.
          + Patched Path.followLink to follow links under cygwin in
            addition to Unix (suggested by Matt Swift).
          + Small change to the storeRootsName function, suggested by
            bliviero at ichips.intel.com, to fix a problem in unison with
            the `rootalias' option, which allows you to tell unison that
            two roots contain the same files. Rootalias was being applied
            after the hosts were sorted, so it wouldn't work properly in
            all cases.
          + Incorporated a fix by Dmitry Bely for setting utimes of
            read-only files on Win32 systems.
     * Installation / portability:
          + Unison now compiles with OCaml version 3.07 and later out of
            the box.
          + Makefile.OCaml fixed to compile out of the box under OpenBSD.
          + a few additional ports (e.g. OpenBSD, Zaurus/IPAQ) are now
            mentioned in the documentation
          + Unison can now be installed easily on OSX systems using the
            Fink package manager

   Changes since 2.9.1:
     * Added a preference maxthreads that can be used to limit the number
       of simultaneous file transfers.
     * Added a backupdir preference, which controls where backup files are
       stored.
     * Basic support added for OSX. In particular, Unison now recognizes
       when one of the hosts being synchronized is running OSX and
       switches to a case-insensitive treatment of filenames (i.e., 'foo'
       and 'FOO' are considered to be the same file). (OSX is not yet
       fully working, however: in particular, files with resource forks
       will not be synchronized correctly.)
     * The same hash used to form the archive name is now also added to
       the names of the temp files created during file transfer. The
       reason for this is that, during update detection, we are going to
       silently delete any old temp files that we find along the way, and
       we want to prevent ourselves from deleting temp files belonging to
       other instances of Unison that may be running in parallel, e.g.
       synchronizing with a different host. Thanks to Ruslan Ermilov for
       this suggestion.
     * Several small user interface improvements
     * Documentation
          + FAQ and bug reporting instructions have been split out as
            separate HTML pages, accessible directly from the unison web
            page.
          + Additions to FAQ, in particular suggestions about performance
            tuning.
     * Makefile
          + Makefile.OCaml now sets UISTYLE=text or UISTYLE=gtk
            automatically, depending on whether it finds lablgtk installed
          + Unison should now compile "out of the box" under OSX

   Changes since 2.8.1:
     * Changing profile works again under Windows
     * File movement optimization: Unison now tries to use local copy
       instead of transfer for moved or copied files. It is controlled by
       a boolean option "xferbycopying".
     * Network statistics window (transfer rate, amount of data
       transferred). [NB: not available in Windows-Cygwin version.]
     * symlinks work under the cygwin version (which is dynamically
       linked).
     * Fixed potential deadlock when synchronizing between Windows and
       Unix
     * Small improvements:
          + If neither the USERPROFILE nor the HOME environment variables
            are set, then Unison will put its temporary commit log (called
            DANGER.README) into the directory named by the UNISON
            environment variable, if any; otherwise it will use C:.
          + alternative set of values for fastcheck: yes = true; no =
            false; default = auto.
          + -silent implies -contactquietly
     * Source code:
          + Code reorganization and tidying. (Started breaking up some of
            the basic utility modules so that the non-unison-specific
            stuff can be made available for other projects.)
          + several Makefile and docs changes (for release);
          + further comments in "update.ml";
          + connection information is not stored in global variables
            anymore.

   Changes since 2.7.78:
     * Small bugfix to textual user interface under Unix (to avoid leaving
       the terminal in a bad state where it would not echo inputs after
       Unison exited).

   Changes since 2.7.39:
     * Improvements to the main web page (stable and beta version docs are
       now both accessible).
     * User manual revised.
     * Added some new preferences:
          + "sshcmd" and "rshcmd" for specifying paths to ssh and rsh
            programs.
          + "contactquietly" for suppressing the "contacting server"
            message during Unison startup (under the graphical UI).
     * Bug fixes:
          + Fixed small bug in UI that neglected to change the displayed
            column headers if loading a new profile caused the roots to
            change.
          + Fixed a bug that would put the text UI into an infinite loop
            if it encountered a conflict when run in batch mode.
          + Added some code to try to fix the display of non-Ascii
            characters in filenames on Windows systems in the GTK UI.
            (This code is currently untested--if you're one of the people
            that had reported problems with display of non-ascii
            filenames, we'd appreciate knowing if this actually fixes
            things.)
          + `-prefer/-force newer' works properly now. (The bug was
            reported by Sebastian Urbaniak and Sean Fulton.)
     * User interface and Unison behavior:
          + Renamed `Proceed' to `Go' in the graphical UI.
          + Added exit status for the textual user interface.
          + Paths that are not synchronized because of conflicts or errors
            during update detection are now noted in the log file.
          + [END] messages in log now use a briefer format
          + Changed the text UI startup sequence so that ./unison -ui text
            will use the default profile instead of failing.
          + Made some improvements to the error messages.
          + Added some debugging messages to remote.ml.

   Changes since 2.7.7:
     * Incorporated, once again, a multi-threaded transport sub-system. It
       transfers several files at the same time, thereby making much more
       effective use of available network bandwidth. Unlike the earlier
       attempt, this time we do not rely on the native thread library of
       OCaml. Instead, we implement a light-weight, non-preemptive
       multi-thread library in OCaml directly. This version appears
       stable.
       Some adjustments to unison are made to accommodate the
       multi-threaded version. These include, in particular, changes to
       the user interface and logging, for example:
          + Two log entries for each transferring task, one for the
            beginning, one for the end.
          + Suppressed warning messages against removing temp files left
            by a previous unison run, because warning does not work nicely
            under multi-threading. The temp file names are made less
            likely to coincide with the name of a file created by the
            user. They take the form
            .#<filename>.<serial>.unison.tmp. [N.b. This was later changed
            to .unison.<filename>.<serial>.unison.tmp.]
     * Added a new command to the GTK user interface: pressing 'f' causes
       Unison to start a new update detection phase, using as paths just
       those paths that have been detected as changed and not yet marked
       as successfully completed. Use this command to quickly restart
       Unison on just the set of paths still needing attention after a
       previous run.
     * Made the ignorecase preference user-visible, and changed the
       initialization code so that it can be manually set to true, even if
       neither host is running Windows. (This may be useful, e.g., when
       using Unison running on a Unix system with a FAT volume mounted.)
     * Small improvements and bug fixes:
          + Errors in preference files now generate fatal errors rather
            than warnings at startup time. (I.e., you can't go on from
            them.) Also, we fixed a bug that was preventing these warnings
            from appearing in the text UI, so some users who have been
            running (unsuspectingly) with garbage in their prefs files may
            now get error reports.
          + Error reporting for preference files now provides file name
            and line number.
          + More intelligible message in the case of identical change to
            the same files: "Nothing to do: replicas have been changed
            only in identical ways since last sync."
          + Files with prefix '.#' excluded when scanning for preference
            files.
          + Rsync instructions are send directly instead of first
            marshaled.
          + Won't try forever to get the fingerprint of a continuously
            changing file: unison will give up after certain number of
            retries.
          + Other bug fixes, including the one reported by Peter Selinger
            (force=older preference not working).
     * Compilation:
          + Upgraded to the new OCaml 3.04 compiler, with the LablGtk
            1.2.3 library (patched version used for compiling under
            Windows).
          + Added the option to compile unison on the Windows platform
            with Cygwin GNU C compiler. This option only supports building
            dynamically linked unison executables.

   Changes since 2.7.4:
     * Fixed a silly (but debilitating) bug in the client startup
       sequence.

   Changes since 2.7.1:
     * Added addprefsto preference, which (when set) controls which
       preference file new preferences (e.g. new ignore patterns) are
       added to.
     * Bug fix: read the initial connection header one byte at a time, so
       that we don't block if the header is shorter than expected. (This
       bug did not affect normal operation -- it just made it hard to tell
       when you were trying to use Unison incorrectly with an old version
       of the server, since it would hang instead of giving an error
       message.)

   Changes since 2.6.59:
     * Changed fastcheck from a boolean to a string preference. Its legal
       values are yes (for a fast check), no (for a safe check), or
       default (for a fast check--which also happens to be safe--when
       running on Unix and a safe check when on Windows). The default is
       default.
     * Several preferences have been renamed for consistency. All
       preference names are now spelled out in lowercase. For backward
       compatibility, the old names still work, but they are not mentioned
       in the manual any more.
     * The temp files created by the 'diff' and 'merge' commands are now
       named by prepending a new prefix to the file name, rather than
       appending a suffix. This should avoid confusing diff/merge programs
       that depend on the suffix to guess the type of the file contents.
     * We now set the keepalive option on the server socket, to make sure
       that the server times out if the communication link is unexpectedly
       broken.
     * Bug fixes:
          + When updating small files, Unison now closes the destination
            file.
          + File permissions are properly updated when the file is behind
            a followed link.
          + Several other small fixes.

   Changes since 2.6.38:
     * Major Windows performance improvement!
       We've added a preference fastcheck that makes Unison look only at a
       file's creation time and last-modified time to check whether it has
       changed. This should result in a huge speedup when checking for
       updates in large replicas.
       When this switch is set, Unison will use file creation times as
       'pseudo inode numbers' when scanning Windows replicas for updates,
       instead of reading the full contents of every file. This may cause
       Unison to miss propagating an update if the create time,
       modification time, and length of the file are all unchanged by the
       update (this is not easy to achieve, but it can be done). However,
       Unison will never overwrite such an update with a change from the
       other replica, since it always does a safe check for updates just
       before propagating a change. Thus, it is reasonable to use this
       switch most of the time and occasionally run Unison once with
       fastcheck set to false, if you are worried that Unison may have
       overlooked an update.
       Warning: This change is has not yet been thoroughly field-tested.
       If you set the fastcheck preference, pay careful attention to what
       Unison is doing.
     * New functionality: centralized backups and merging
          + This version incorporates two pieces of major new
            functionality, implemented by Sylvain Roy during a summer
            internship at Penn: a centralized backup facility that keeps a
            full backup of (selected files in) each replica, and a merging
            feature that allows Unison to invoke an external file-merging
            tool to resolve conflicting changes to individual files.
          + Centralized backups:
               o Unison now maintains full backups of the
                 last-synchronized versions of (some of) the files in each
                 replica; these function both as backups in the usual
                 sense and as the "common version" when invoking external
                 merge programs.
               o The backed up files are stored in a directory
                 /.unison/backup on each host. (The name of this directory
                 can be changed by setting the environment variable
                 UNISONBACKUPDIR.)
               o The predicate backup controls which files are actually
                 backed up: giving the preference 'backup = Path *' causes
                 backing up of all files.
               o Files are added to the backup directory whenever unison
                 updates its archive. This means that
                    # When unison reconstructs its archive from scratch
                      (e.g., because of an upgrade, or because the archive
                      files have been manually deleted), all files will be
                      backed up.
                    # Otherwise, each file will be backed up the first
                      time unison propagates an update for it.
               o The preference backupversions controls how many previous
                 versions of each file are kept. The default is 2 (i.e.,
                 the last synchronized version plus one backup).
               o For backward compatibility, the backups preference is
                 also still supported, but backup is now preferred.
               o It is OK to manually delete files from the backup
                 directory (or to throw away the directory itself). Before
                 unison uses any of these files for anything important, it
                 checks that its fingerprint matches the one that it
                 expects.
          + Merging:
               o Both user interfaces offer a new 'merge' command, invoked
                 by pressing 'm' (with a changed file selected).
               o The actual merging is performed by an external program.
                 The preferences merge and merge2 control how this program
                 is invoked. If a backup exists for this file (see the
                 backup preference), then the merge preference is used for
                 this purpose; otherwise merge2 is used. In both cases,
                 the value of the preference should be a string
                 representing the command that should be passed to a shell
                 to invoke the merge program. Within this string, the
                 special substrings CURRENT1, CURRENT2, NEW, and OLD may
                 appear at any point. Unison will substitute these as
                 follows before invoking the command:
                    # CURRENT1 is replaced by the name of the local copy
                      of the file;
                    # CURRENT2 is replaced by the name of a temporary
                      file, into which the contents of the remote copy of
                      the file have been transferred by Unison prior to
                      performing the merge;
                    # NEW is replaced by the name of a temporary file that
                      Unison expects to be written by the merge program
                      when it finishes, giving the desired new contents of
                      the file; and
                    # OLD is replaced by the name of the backed up copy of
                      the original version of the file (i.e., its state at
                      the end of the last successful run of Unison), if
                      one exists (applies only to merge, not merge2).
                 For example, on Unix systems setting the merge preference
                 to
   merge = diff3 -m CURRENT1 OLD CURRENT2 > NEW

                 will tell Unison to use the external diff3 program for
                 merging.
                 A large number of external merging programs are
                 available. For example, emacs users may find the
                 following convenient:
    merge2 = emacs -q --eval '(ediff-merge-files "CURRENT1" "CURRENT2"
               nil "NEW")'
    merge = emacs -q --eval '(ediff-merge-files-with-ancestor
               "CURRENT1" "CURRENT2" "OLD" nil "NEW")'

                 (These commands are displayed here on two lines to avoid
                 running off the edge of the page. In your preference
                 file, each should be written on a single line.)
               o If the external program exits without leaving any file at
                 the path NEW, Unison considers the merge to have failed.
                 If the merge program writes a file called NEW but exits
                 with a non-zero status code, then Unison considers the
                 merge to have succeeded but to have generated conflicts.
                 In this case, it attempts to invoke an external editor so
                 that the user can resolve the conflicts. The value of the
                 editor preference controls what editor is invoked by
                 Unison. The default is emacs.
               o Please send us suggestions for other useful values of the
                 merge2 and merge preferences - we'd like to give several
                 examples in the manual.
     * Smaller changes:
          + When one preference file includes another, unison no longer
            adds the suffix '.prf' to the included file by default. If a
            file with precisely the given name exists in the .unison
            directory, it will be used; otherwise Unison will add .prf, as
            it did before. (This change means that included preference
            files can be named blah.include instead of blah.prf, so that
            unison will not offer them in its 'choose a preference file'
            dialog.)
          + For Linux systems, we now offer both a statically linked and a
            dynamically linked executable. The static one is larger, but
            will probably run on more systems, since it doesn't depend on
            the same versions of dynamically linked library modules being
            available.
          + Fixed the force and prefer preferences, which were getting the
            propagation direction exactly backwards.
          + Fixed a bug in the startup code that would cause unison to
            crash when the default profile (~/.unison/default.prf) does
            not exist.
          + Fixed a bug where, on the run when a profile is first created,
            Unison would confusingly display the roots in reverse order in
            the user interface.
     * For developers:
          + We've added a module dependency diagram to the source
            distribution, in src/DEPENDENCIES.ps, to help new prospective
            developers with navigating the code.

   Changes since 2.6.11:
     * INCOMPATIBLE CHANGE: Archive format has changed.
     * INCOMPATIBLE CHANGE: The startup sequence has been completely
       rewritten and greatly simplified. The main user-visible change is
       that the defaultpath preference has been removed. Its effect can be
       approximated by using multiple profiles, with include directives to
       incorporate common settings. All uses of defaultpath in existing
       profiles should be changed to path.
       Another change in startup behavior that will affect some users is
       that it is no longer possible to specify roots both in the profile
       and on the command line.
       You can achieve a similar effect, though, by breaking your profile
       into two:
  default.prf =
      root = blah
      root = foo
      include common

  common.prf =
      <everything else>

       Now do
  unison common root1 root2

       when you want to specify roots explicitly.
     * The -prefer and -force options have been extended to allow users to
       specify that files with more recent modtimes should be propagated,
       writing either -prefer newer or -force newer. (For symmetry, Unison
       will also accept -prefer older or -force older.) The -force
       older/newer options can only be used when -times is also set.
       The graphical user interface provides access to these facilities on
       a one-off basis via the Actions menu.
     * Names of roots can now be "aliased" to allow replicas to be
       relocated without changing the name of the archive file where
       Unison stores information between runs. (This feature is for
       experts only. See the "Archive Files" section of the manual for
       more information.)
     * Graphical user-interface:
          + A new command is provided in the Synchronization menu for
            switching to a new profile without restarting Unison from
            scratch.
          + The GUI also supports one-key shortcuts for commonly used
            profiles. If a profile contains a preference of the form 'key
            = n', where n is a single digit, then pressing this key will
            cause Unison to immediately switch to this profile and begin
            synchronization again from scratch. (Any actions that may have
            been selected for a set of changes currently being displayed
            will be discarded.)
          + Each profile may include a preference 'label = <string>'
            giving a descriptive string that described the options
            selected in this profile. The string is listed along with the
            profile name in the profile selection dialog, and displayed in
            the top-right corner of the main Unison window.
     * Minor:
          + Fixed a bug that would sometimes cause the 'diff' display to
            order the files backwards relative to the main user interface.
            (Thanks to Pascal Brisset for this fix.)
          + On Unix systems, the graphical version of Unison will check
            the DISPLAY variable and, if it is not set, automatically fall
            back to the textual user interface.
          + Synchronization paths (path preferences) are now matched
            against the ignore preferences. So if a path is both specified
            in a path preference and ignored, it will be skipped.
          + Numerous other bugfixes and small improvements.

   Changes since 2.6.1:
     * The synchronization of modification times has been disabled for
       directories.
     * Preference files may now include lines of the form include <name>,
       which will cause name.prf to be read at that point.
     * The synchronization of permission between Windows and Unix now
       works properly.
     * A binding CYGWIN=binmode in now added to the environment so that
       the Cygwin port of OpenSSH works properly in a non-Cygwin context.
     * The servercmd and addversionno preferences can now be used
       together: -addversionno appends an appropriate -NNN to the server
       command, which is found by using the value of the -servercmd
       preference if there is one, or else just unison.
     * Both '-pref=val' and '-pref val' are now allowed for boolean
       values. (The former can be used to set a preference to false.)
     * Lot of small bugs fixed.

   Changes since 2.5.31:
     * The log preference is now set to true by default, since the log
       file seems useful for most users.
     * Several miscellaneous bugfixes (most involving symlinks).

   Changes since 2.5.25:
     * INCOMPATIBLE CHANGE: Archive format has changed (again).
     * Several significant bugs introduced in 2.5.25 have been fixed.

   Changes since 2.5.1:
     * INCOMPATIBLE CHANGE: Archive format has changed. Make sure you
       synchronize your replicas before upgrading, to avoid spurious
       conflicts. The first sync after upgrading will be slow.
     * New functionality:
          + Unison now synchronizes file modtimes, user-ids, and
            group-ids.
            These new features are controlled by a set of new preferences,
            all of which are currently false by default.
               o When the times preference is set to true, file
                 modification times are propaged. (Because the
                 representations of time may not have the same granularity
                 on both replicas, Unison may not always be able to make
                 the modtimes precisely equal, but it will get them as
                 close as the operating systems involved allow.)
               o When the owner preference is set to true, file ownership
                 information is synchronized.
               o When the group preference is set to true, group
                 information is synchronized.
               o When the numericIds preference is set to true, owner and
                 group information is synchronized numerically. By
                 default, owner and group numbers are converted to names
                 on each replica and these names are synchronized. (The
                 special user id 0 and the special group 0 are never
                 mapped via user/group names even if this preference is
                 not set.)
          + Added an integer-valued preference perms that can be used to
            control the propagation of permission bits. The value of this
            preference is a mask indicating which permission bits should
            be synchronized. It is set by default to 0o1777: all bits but
            the set-uid and set-gid bits are synchronised (synchronizing
            theses latter bits can be a security hazard). If you want to
            synchronize all bits, you can set the value of this preference
            to -1.
          + Added a log preference (default false), which makes Unison
            keep a complete record of the changes it makes to the
            replicas. By default, this record is written to a file called
            unison.log in the user's home directory (the value of the HOME
            environment variable). If you want it someplace else, set the
            logfile preference to the full pathname you want Unison to
            use.
          + Added an ignorenot preference that maintains a set of patterns
            for paths that should definitely not be ignored, whether or
            not they match an ignore pattern. (That is, a path will now be
            ignored iff it matches an ignore pattern and does not match
            any ignorenot patterns.)
     * User-interface improvements:
          + Roots are now displayed in the user interface in the same
            order as they were given on the command line or in the
            preferences file.
          + When the batch preference is set, the graphical user interface
            no longer waits for user confirmation when it displays a
            warning message: it simply pops up an advisory window with a
            Dismiss button at the bottom and keeps on going.
          + Added a new preference for controlling how many status
            messages are printed during update detection: statusdepth
            controls the maximum depth for paths on the local machine
            (longer paths are not displayed, nor are non-directory paths).
            The value should be an integer; default is 1.
          + Removed the trace and silent preferences. They did not seem
            very useful, and there were too many preferences for
            controlling output in various ways.
          + The text UI now displays just the default command (the one
            that will be used if the user just types <return>) instead of
            all available commands. Typing ? will print the full list of
            possibilities.
          + The function that finds the canonical hostname of the local
            host (which is used, for example, in calculating the name of
            the archive file used to remember which files have been
            synchronized) normally uses the gethostname operating system
            call. However, if the environment variable UNISONLOCALHOSTNAME
            is set, its value will now be used instead. This makes it
            easier to use Unison in situations where a machine's name
            changes frequently (e.g., because it is a laptop and gets
            moved around a lot).
          + File owner and group are now displayed in the "detail window"
            at the bottom of the screen, when unison is configured to
            synchronize them.
     * For hackers:
          + Updated to Jacques Garrigue's new version of lablgtk, which
            means we can throw away our local patched version.
            If you're compiling the GTK version of unison from sources,
            you'll need to update your copy of lablgtk to the developers
            release. (Warning: installing lablgtk under Windows is
            currently a bit challenging.)
          + The TODO.txt file (in the source distribution) has been
            cleaned up and reorganized. The list of pending tasks should
            be much easier to make sense of, for people that may want to
            contribute their programming energies. There is also a
            separate file BUGS.txt for open bugs.
          + The Tk user interface has been removed (it was not being
            maintained and no longer compiles).
          + The debug preference now prints quite a bit of additional
            information that should be useful for identifying sources of
            problems.
          + The version number of the remote server is now checked right
            away during the connection setup handshake, rather than later.
            (Somebody sent a bug report of a server crash that turned out
            to come from using inconsistent versions: better to check this
            earlier and in a way that can't crash either client or
            server.)
          + Unison now runs correctly on 64-bit architectures (e.g. Alpha
            linux). We will not be distributing binaries for these
            architectures ourselves (at least for a while) but if someone
            would like to make them available, we'll be glad to provide a
            link to them.
     * Bug fixes:
          + Pattern matching (e.g. for ignore) is now case-insensitive
            when Unison is in case-insensitive mode (i.e., when one of the
            replicas is on a windows machine).
          + Some people had trouble with mysterious failures during
            propagation of updates, where files would be falsely reported
            as having changed during synchronization. This should be
            fixed.
          + Numerous smaller fixes.

   Changes since 2.4.1:
     * Added a number of 'sorting modes' for the user interface. By
       default, conflicting changes are displayed at the top, and the rest
       of the entries are sorted in alphabetical order. This behavior can
       be changed in the following ways:
          + Setting the sortnewfirst preference to true causes newly
            created files to be displayed before changed files.
          + Setting sortbysize causes files to be displayed in increasing
            order of size.
          + Giving the preference sortfirst=<pattern> (where <pattern> is
            a path descriptor in the same format as 'ignore' and 'follow'
            patterns, causes paths matching this pattern to be displayed
            first.
          + Similarly, giving the preference sortlast=<pattern> causes
            paths matching this pattern to be displayed last.
       The sorting preferences are described in more detail in the user
       manual. The sortnewfirst and sortbysize flags can also be accessed
       from the 'Sort' menu in the grpahical user interface.
     * Added two new preferences that can be used to change unison's
       fundamental behavior to make it more like a mirroring tool instead
       of a synchronizer.
          + Giving the preference prefer with argument <root> (by adding
            -prefer <root> to the command line or prefer=<root>) to your
            profile) means that, if there is a conflict, the contents of
            <root> should be propagated to the other replica (with no
            questions asked). Non-conflicting changes are treated as
            usual.
          + Giving the preference force with argument <root> will make
            unison resolve all differences in favor of the given root,
            even if it was the other replica that was changed.
       These options should be used with care! (More information is
       available in the manual.)
     * Small changes:
          + Changed default answer to 'Yes' in all two-button dialogs in
            the graphical interface (this seems more intuitive).
          + The rsync preference has been removed (it was used to activate
            rsync compression for file transfers, but rsync compression is
            now enabled by default).
          + In the text user interface, the arrows indicating which
            direction changes are being propagated are printed differently
            when the user has overridden Unison's default recommendation
            (====> instead of ---->). This matches the behavior of the
            graphical interface, which displays such arrows in a different
            color.
          + Carriage returns (Control-M's) are ignored at the ends of
            lines in profiles, for Windows compatibility.
          + All preferences are now fully documented in the user manual.

   Changes since 2.3.12:
     * INCOMPATIBLE CHANGE: Archive format has changed. Make sure you
       synchronize your replicas before upgrading, to avoid spurious
       conflicts. The first sync after upgrading will be slow.
     * New/improved functionality:
          + A new preference -sortbysize controls the order in which
            changes are displayed to the user: when it is set to true, the
            smallest changed files are displayed first. (The default
            setting is false.)
          + A new preference -sortnewfirst causes newly created files to
            be listed before other updates in the user interface.
          + We now allow the ssh protocol to specify a port.
          + Incompatible change: The unison: protocol is deprecated, and
            we added file: and socket:. You may have to modify your
            profiles in the .unison directory. If a replica is specified
            without an explicit protocol, we now assume it refers to a
            file. (Previously "//saul/foo" meant to use SSH to connect to
            saul, then access the foo directory. Now it means to access
            saul via a remote file mechanism such as samba; the old effect
            is now achieved by writing ssh://saul/foo.)
          + Changed the startup sequence for the case where roots are
            given but no profile is given on the command line. The new
            behavior is to use the default profile (creating it if it does
            not exist), and temporarily override its roots. The manual
            claimed that this case would work by reading no profile at
            all, but AFAIK this was never true.
          + In all user interfaces, files with conflicts are always listed
            first
          + A new preference 'sshversion' can be used to control which
            version of ssh should be used to connect to the server. Legal
            values are 1 and 2. (Default is empty, which will make unison
            use whatever version of ssh is installed as the default 'ssh'
            command.)
          + The situation when the permissions of a file was updated the
            same on both side is now handled correctly (we used to report
            a spurious conflict)
     * Improvements for the Windows version:
          + The fact that filenames are treated case-insensitively under
            Windows should now be handled correctly. The exact behavior is
            described in the cross-platform section of the manual.
          + It should be possible to synchronize with Windows shares,
            e.g., //host/drive/path.
          + Workarounds to the bug in syncing root directories in Windows.
            The most difficult thing to fix is an ocaml bug: Unix.opendir
            fails on c: in some versions of Windows.
     * Improvements to the GTK user interface (the Tk interface is no
       longer being maintained):
          + The UI now displays actions differently (in blue) when they
            have been explicitly changed by the user from Unison's default
            recommendation.
          + More colorful appearance.
          + The initial profile selection window works better.
          + If any transfers failed, a message to this effect is displayed
            along with 'Synchronization complete' at the end of the
            transfer phase (in case they may have scrolled off the top).
          + Added a global progress meter, displaying the percentage of
            total bytes that have been transferred so far.
     * Improvements to the text user interface:
          + The file details will be displayed automatically when a
            conflict is been detected.
          + when a warning is generated (e.g. for a temporary file left
            over from a previous run of unison) Unison will no longer wait
            for a response if it is running in -batch mode.
          + The UI now displays a short list of possible inputs each time
            it waits for user interaction.
          + The UI now quits immediately (rather than looping back and
            starting the interaction again) if the user presses 'q' when
            asked whether to propagate changes.
          + Pressing 'g' in the text user interface will proceed
            immediately with propagating updates, without asking any more
            questions.
     * Documentation and installation changes:
          + The manual now includes a FAQ, plus sections on common
            problems and on tricks contributed by users.
          + Both the download page and the download directory explicitly
            say what are the current stable and beta-test version numbers.
          + The OCaml sources for the up-to-the-minute developers' version
            (not guaranteed to be stable, or even to compile, at any given
            time!) are now available from the download page.
          + Added a subsection to the manual describing cross-platform
            issues (case conflicts, illegal filenames)
     * Many small bug fixes and random improvements.

   Changes since 2.3.1:
     * Several bug fixes. The most important is a bug in the rsync module
       that would occasionally cause change propagation to fail with a
       'rename' error.

   Changes since 2.2:
     * The multi-threaded transport system is now disabled by default. (It
       is not stable enough yet.)
     * Various bug fixes.
     * A new experimental feature:
       The final component of a -path argument may now be the wildcard
       specifier *. When Unison sees such a path, it expands this path on
       the client into into the corresponding list of paths by listing the
       contents of that directory.
       Note that if you use wildcard paths from the command line, you will
       probably need to use quotes or a backslash to prevent the * from
       being interpreted by your shell.
       If both roots are local, the contents of the first one will be used
       for expanding wildcard paths. (Nb: this is the first one after the
       canonization step - i.e., the one that is listed first in the user
       interface - not the one listed first on the command line or in the
       preferences file.)

   Changes since 2.1:
     * The transport subsystem now includes an implementation by Sylvain
       Gommier and Norman Ramsey of Tridgell and Mackerras's rsync
       protocol. This protocol achieves much faster transfers when only a
       small part of a large file has been changed by sending just diffs.
       This feature is mainly helpful for transfers over slow links--on
       fast local area networks it can actually degrade performance--so we
       have left it off by default. Start unison with the -rsync option
       (or put rsync=true in your preferences file) to turn it on.
     * "Progress bars" are now displayed during remote file transfers,
       showing what percentage of each file has been transferred so far.
     * The version numbering scheme has changed. New releases will now be
       have numbers like 2.2.30, where the second component is incremented
       on every significant public release and the third component is the
       "patch level."
     * Miscellaneous improvements to the GTK-based user interface.
     * The manual is now available in PDF format.
     * We are experimenting with using a multi-threaded transport
       subsystem to transfer several files at the same time, making much
       more effective use of available network bandwidth. This feature is
       not completely stable yet, so by default it is disabled in the
       release version of Unison.
       If you want to play with the multi-threaded version, you'll need to
       recompile Unison from sources (as described in the documentation),
       setting the THREADS flag in Makefile.OCaml to true. Make sure that
       your OCaml compiler has been installed with the -with-pthreads
       configuration option. (You can verify this by checking whether the
       file threads/threads.cma in the OCaml standard library directory
       contains the string -lpthread near the end.)

   Changes since 1.292:
     * Reduced memory footprint (this is especially important during the
       first run of unison, where it has to gather information about all
       the files in both repositories).
     * Fixed a bug that would cause the socket server under NT to fail
       after the client exits.
     * Added a SHIFT modifier to the Ignore menu shortcut keys in GTK
       interface (to avoid hitting them accidentally).

   Changes since 1.231:
     * Tunneling over ssh is now supported in the Windows version. See the
       installation section of the manual for detailed instructions.
     * The transport subsystem now includes an implementation of the rsync
       protocol, built by Sylvain Gommier and Norman Ramsey. This protocol
       achieves much faster transfers when only a small part of a large
       file has been changed by sending just diffs. The rsync feature is
       off by default in the current version. Use the -rsync switch to
       turn it on. (Nb. We still have a lot of tuning to do: you may not
       notice much speedup yet.)
     * We're experimenting with a multi-threaded transport subsystem,
       written by Jerome Vouillon. The downloadable binaries are still
       single-threaded: if you want to try the multi-threaded version,
       you'll need to recompile from sources. (Say make THREADS=true.)
       Native thread support from the compiler is required. Use the option
       -threads N to select the maximal number of concurrent threads
       (default is 5). Multi-threaded and single-threaded clients/servers
       can interoperate.
     * A new GTK-based user interface is now available, thanks to Jacques
       Garrigue. The Tk user interface still works, but we'll be shifting
       development effort to the GTK interface from now on.
     * OCaml 3.00 is now required for compiling Unison from sources. The
       modules uitk and myfileselect have been changed to use labltk
       instead of camltk. To compile the Tk interface in Windows, you must
       have ocaml-3.00 and tk8.3. When installing tk8.3, put it in c:\Tcl
       rather than the suggested c:\Program Files\Tcl, and be sure to
       install the headers and libraries (which are not installed by
       default).
     * Added a new -addversionno switch, which causes unison to use
       unison-<currentversionnumber> instead of just unison as the remote
       server command. This allows multiple versions of unison to coexist
       conveniently on the same server: whichever version is run on the
       client, the same version will be selected on the server.

   Changes since 1.219:
     * INCOMPATIBLE CHANGE: Archive format has changed. Make sure you
       synchronize your replicas before upgrading, to avoid spurious
       conflicts. The first sync after upgrading will be slow.
     * This version fixes several annoying bugs, including:
          + Some cases where propagation of file permissions was not
            working.
          + umask is now ignored when creating directories
          + directories are create writable, so that a read-only directory
            and its contents can be propagated.
          + Handling of warnings generated by the server.
          + Synchronizing a path whose parent is not a directory on both
            sides is now flagged as erroneous.
          + Fixed some bugs related to symnbolic links and nonexistent
            roots.
               o When a change (deletion or new contents) is propagated
                 onto a 'follow'ed symlink, the file pointed to by the
                 link is now changed. (We used to change the link itself,
                 which doesn't fit our assertion that 'follow' means the
                 link is completely invisible)
               o When one root did not exist, propagating the other root
                 on top of it used to fail, because unison could not
                 calculate the working directory into which to write
                 changes. This should be fixed.
     * A human-readable timestamp has been added to Unison's archive
       files.
     * The semantics of Path and Name regular expressions now correspond
       better.
     * Some minor improvements to the text UI (e.g. a command for going
       back to previous items)
     * The organization of the export directory has changed -- should be
       easier to find / download things now.

   Changes since 1.200:
     * INCOMPATIBLE CHANGE: Archive format has changed. Make sure you
       synchronize your replicas before upgrading, to avoid spurious
       conflicts. The first sync after upgrading will be slow.
     * This version has not been tested extensively on Windows.
     * Major internal changes designed to make unison safer to run at the
       same time as the replicas are being changed by the user.
     * Internal performance improvements.

   Changes since 1.190:
     * INCOMPATIBLE CHANGE: Archive format has changed. Make sure you
       synchronize your replicas before upgrading, to avoid spurious
       conflicts. The first sync after upgrading will be slow.
     * A number of internal functions have been changed to reduce the
       amount of memory allocation, especially during the first
       synchronization. This should help power users with very big
       replicas.
     * Reimplementation of low-level remote procedure call stuff, in
       preparation for adding rsync-like smart file transfer in a later
       release.
     * Miscellaneous bug fixes.

   Changes since 1.180:
     * INCOMPATIBLE CHANGE: Archive format has changed. Make sure you
       synchronize your replicas before upgrading, to avoid spurious
       conflicts. The first sync after upgrading will be slow.
     * Fixed some small bugs in the interpretation of ignore patterns.
     * Fixed some problems that were preventing the Windows version from
       working correctly when click-started.
     * Fixes to treatment of file permissions under Windows, which were
       causing spurious reports of different permissions when
       synchronizing between windows and unix systems.
     * Fixed one more non-tail-recursive list processing function, which
       was causing stack overflows when synchronizing very large replicas.

   Changes since 1.169:
     * The text user interface now provides commands for ignoring files.
     * We found and fixed some more non-tail-recursive list processing
       functions. Some power users have reported success with very large
       replicas.
     * INCOMPATIBLE CHANGE: Files ending in .tmp are no longer ignored
       automatically. If you want to ignore such files, put an appropriate
       ignore pattern in your profile.
     * INCOMPATIBLE CHANGE: The syntax of ignore and follow patterns has
       changed. Instead of putting a line of the form
                 ignore = <regexp>

       in your profile (.unison/default.prf), you should put:
                 ignore = Regex <regexp>

       Moreover, two other styles of pattern are also recognized:
                 ignore = Name <name>

       matches any path in which one component matches <name>, while
                 ignore = Path <path>

       matches exactly the path <path>.
       Standard "globbing" conventions can be used in <name> and <path>:
          + a ? matches any single character except /
          + a * matches any sequence of characters not including /
          + [xyz] matches any character from the set {x, y, z }
          + {a,bb,ccc} matches any one of a, bb, or ccc.
       See the user manual for some examples.

   Changes since 1.146:
     * Some users were reporting stack overflows when synchronizing huge
       directories. We found and fixed some non-tail-recursive list
       processing functions, which we hope will solve the problem. Please
       give it a try and let us know.
     * Major additions to the documentation.

   Changes since 1.142:
     * Major internal tidying and many small bugfixes.
     * Major additions to the user manual.
     * Unison can now be started with no arguments - it will prompt
       automatically for the name of a profile file containing the roots
       to be synchronized. This makes it possible to start the graphical
       UI from a desktop icon.
     * Fixed a small bug where the text UI on NT was raising a 'no such
       signal' exception.

   Changes since 1.139:
     * The precompiled windows binary in the last release was compiled
       with an old OCaml compiler, causing propagation of permissions not
       to work (and perhaps leading to some other strange behaviors we've
       heard reports about). This has been corrected. If you're using
       precompiled binaries on Windows, please upgrade.
     * Added a -debug command line flag, which controls debugging of
       various modules. Say -debug XXX to enable debug tracing for module
       XXX, or -debug all to turn on absolutely everything.
     * Fixed a small bug where the text UI on NT was raising a 'no such
       signal' exception.

   Changes since 1.111:
     * INCOMPATIBLE CHANGE: The names and formats of the preference files
       in the .unison directory have changed. In particular:
          + the file "prefs" should be renamed to default.prf
          + the contents of the file "ignore" should be merged into
            default.prf. Each line of the form REGEXP in ignore should
            become a line of the form ignore = REGEXP in default.prf.
     * Unison now handles permission bits and symbolic links. See the
       manual for details.
     * You can now have different preference files in your .unison
       directory. If you start unison like this
             unison profilename

       (i.e. with just one "anonymous" command-line argument), then the
       file ~/.unison/profilename.prf will be loaded instead of
       default.prf.
     * Some improvements to terminal handling in the text user interface
     * Added a switch -killServer that terminates the remote server
       process when the unison client is shutting down, even when using
       sockets for communication. (By default, a remote server created
       using ssh/rsh is terminated automatically, while a socket server is
       left running.)
     * When started in 'socket server' mode, unison prints 'server
       started' on stderr when it is ready to accept connections. (This
       may be useful for scripts that want to tell when a socket-mode
       server has finished initialization.)
     * We now make a nightly mirror of our current internal development
       tree, in case anyone wants an up-to-the-minute version to hack
       around with.
     * Added a file CONTRIB with some suggestions for how to help us make
       Unison better.
