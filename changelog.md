# Release notes
### Version 2.3 Build 1909.2401
#### Solved issues
<li>Program would error out when trying to convert without the --validate_xml option when the xmlschema library wasn't 
installed. Closes #36.</li>

#### Known Limitations
<li>Since the latest version of Huawei Backup (10.0.0.360_OVE) and more importantly Huawei Health (10.0.0.533), the
program is ( / might be, see #35) defunct in its current state. Last version of Huawei Helath app seems to disallow
backups through its properties. Huawei Backup release notes state that encryption of the backup is required.</li>

### Version 2.3 Build 1909.1501
#### New features and changes
<li>Added **--output_file_prefix** command line argument option. You can now add the strftime representation of this argument 
as a prefix to the generated TCX XML file(s). E.g. use %Y-%m-%d- to add human readable year-month-day information in 
the name of the generated TCX file.</li>
<li>Reworked auto-detection of activity types. This was needed for the detection and distinction between pool swimming 
and open water swimming [see also FEATURE #28]. Added internal auto-distinction between walking and running based on
research in https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5435734. For validation purposes, both types will still be
converted as 'Run' in the TCX file (for now) to comply with the Garmin TCX XSD. Closes #31. Closes #24.</li>
<li>The --sport command line argument now disables auto-detection of the type of sport of the activity and also enforces 
the selected type of sport for the conversion.</li>
<li>The values of --sport command line argument have been changed. 'Swim' has been replaced by 'Swim_Pool' or 
'Swim_Open_water'. Please adapt your script(s) or remove the command line argument and try the (improved) auto-detection.</li>
<li>
[FEATURE #28] Alpha support for open water swimming activities. Tested on a single activity file only. SWOLF and speed
data were found to be unreliable / unusable. The conversion relies on the GPS data for this type of activity. We welcome
your feedback if you have any problems or remarks using this feature. Closes #28. 
</li>

#### Solved issues
<li>Bug solved in swimming lap generation that could cause each lap to be generated twice (probably since V2.0 B1908.3101).</li>
<li>Bug solved that could cause the --sport command line argument to get overruled by auto-detection. Closes #30.</li>

#### Known Limitations
<li>Distance calculation during long(er) pause periods can be off due to continuing speed data records
during the pause in the HiTrack file. If you suspect a converted activity to have a wrong distance, you can 
try to recalculate the distance from the activity details screen in Strava for now.</li>

### Version 2.2 Build 1909.0801
#### New features and changes
<li>[FEATURE #21] Conversion of Walking, Running ad Cycling activities without any GPS data is supported. You can now convert 
activities that were recorded using a fitness band only (that has no GPS) without using your phone during the 
activity. Distance calculation is an estimated distance and may differ from the real distance because calculation is
based on (average) speed data during a period of seconds (typically 5 seconds) with a resolution of 1 dm/s. Closes #21.</li>

#### Solved issues

<li>[BUG #27] Solved conversion error due to improper segment handling in some cases of GPS loss and/or pause.</li>
<li>Solved potential conversion error due to improper distance calculation in case the HiTrack file would not contain 
an explicit stop record.</li>

#### Known Limitations
<li>
[FEATURE #28] Open water swimming activities were not and are not supported (yet).
</li>

### Version 2.1 Build 1909.0701
#### New features and changes
<li>More accurate distance calculation in case of GPS loss during an activity is now supported for walking, running and 
cycling activities. The real-time speed data in the Hitrack file is used to determine the distance during GPS loss.
See also #21.</li>

#### Known Limitations
<li>Walking, Running and Cycling activities without any GPS data at all can't be converted (yet) and the conversion will
fail with an error. This might be related to issue #18 (O m distance) reported in pre-version 2. Possible use-case: 
You use your fitness band (that has no GPS) without your phone during an activity. To be solved in a future update.</li>

### Version 2.0 Build 1908.3101
#### Solved issues

<li>[BUG #26] Solved issue causing only a part of the activity to be converted. It was detected in a case where the
activity track contained multiple loops over the same track (and/or on some devices, the records in the Hitrack
file are not in chronological order).</li>

### Version 2.0 Build 1908.2901
#### New features and changes
<li>Changed the auto-detected activity type from 'Walk' to 'Run' for walking or running activities. Please note that the
known limitation from the previous version still exists: no auto-distinction between walking and running activities,
they both are detected as running activities.</li>

#### Solved issues

<li>[BUG #25] Error parsing altitude data from tp=alti records in Hitrack file</li>

### Version 2.0 Build 1908.2201
#### New features and changes
<li><u>Support for swimming activities.</u> In this version duration and distances are supported. SWOLF data is available 
from the HiTrack files but is not exported (yet) since we don't have the information how to pass it in the TCX 
format or if Strava supports it natively.</li>
<li><u>Direct conversion from tarball.</u> It is now possible to convert activities directly from the tarball without the 
need to extract them.</li>
<li><u>Auto activity type detection.</u> Auto detection of running, cycling or swimming activities. There is no auto 
distinction (yet) between walking and running activities. For now, walking activities will be detected as running. The 
activity type can be changed in Strava directly after importing the file.</li>
<li><u>Extended program logging.</u> Ability to log only information messages or to have a more extensive debug logging.</li>
<li><u>Restructured and new command line options.</u> This includes new options for direct tarball processing (--tar and 
--from_date), file processing (--file), forcing the sport type (--sport), setting the pool length for swim
activities (--pool_length) and general options to set the directory for extracted and converted files 
(--output_dir), the logging detail (--log_level) and the optional validation of converted TCX XML files 
(--validate_xml)</li>
<li>The source code underwent a restructuring to facilitate the new features.</li>

#### Solved issues
<li>Step frequency corrected.<br>Strava displays steps/minute in app but reads the value in the imported file as 
strides/minute. As a consequence, imported step frequency information from previous versions was 2 times the real value.</li>

#### Known Limitations
<li>Auto distinction between walking and running activities is not included in this version.</li>
<li>For walking and swimming activities, in this version the correct sport type must be manually changed in Strava directly 
after importing the TCX file.</li>
<li>Distance calculation may be incorrect in this version when GPS signal is lost during a longer period or there is no
GPS signal at all.</li>
<li>Due to the very nature of the available speed data (minimum resolution is 1 dm/s) for swimming activities, swimming
distances are an estimation (unless the --pool_length option is used) and there might be a second difference in the
actual lap times versus the ones shown in the Huawei Health app.</li>
<li>There is no direct upload functionality to upload the converted activities to Strava in this version.</li>
<li>This program is dependent on the availability of the temporary/intermediate 'HiTrack' files in the Huawei Health app.</li>
<li>This program is dependent on the Huawei Backup to take an unencrypted backup of the Huawei Health app data.</li>
<li>This program was not tested on Python versions prior to version 3.7.3.</li>
