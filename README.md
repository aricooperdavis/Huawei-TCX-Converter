<img align="left" width="80" height="80" src="./Development Tools/icon.png" alt="Huawei TCX Converter icon">

# Huawei TCX Converter
A makeshift python tool that generates TCX files from Huawei HiTrack files.

---

This project is now archived. It has been succedded by [Hitrava](https://github.com/CTHRU/Hitrava) which performs the same job but better.

---

Users of Huawei Watches/Bands sync their fitness data with the Huawei Health App. It is [notoriously difficult](https://uk.community.huawei.com/software-17/huawei-health-integration-with-other-services-1198/index3.html) to get the data out of this app, but [through some cunning](https://forum.xda-developers.com/smartwatch/huawei-watch/huawei-watch-gt-export-data-health-t3874330) you can find `HiTrack` files which seem to contain some run data. This program allows you to take these files and generate `.TCX` files for use in your tracking app of choice (e.g. Strava). The outputted `.TCX` files will contain timestamped GPS, altitude, heart-rate, and cadence data where available.

**N.B. Huawei have caught onto us, and you now need a rooted phone to be able to use this method. Instead, this program can also process json data produced on request from Huawei. See ["How to get motion data JSON"](https://github.com/aricooperdavis/Huawei-TCX-Converter#how-to-get-motion-data-json) for more info.**

## How to get the HiTrack Files

- Open the Huawei Health app and open the exercise that you want to convert to view it's trajectory. This ensures that its HiTrack file is generated.

If you have a **rooted** phone you can simply navigate to: `data/data/com.huawei.health/files/` where you should find a number of files prefixed `HiTrack`.

If you have an **unrooted** phone then:
**N.B. You may need an old version of the Huawei Health app to use this method.**
- Download the [Huawei Backup App](https://play.google.com/store/apps/details?id=com.huawei.KoBackup&hl=en_GB) onto your phone.
- Start a new **unencrypted** backup of the Huawei Health app data to your external storage (SD Card)
- Navigate to `Huawei/Backup/***/backupFiles/***/` and copy `com.huawei.health.tar` to your computer.
- Unzip the file and navigate to `com.huawei.health/files/` and you should should see a number of `HiTrack` files.

## How to get motion data JSON
This program can also process the data provided by Huawei when you request a copy of your personal data. Request this as follows:
- Enabled your Huawei account in the app.
- Tap the "Me" button in the lower right-hand corner, then tap on your account name on top of 
the screen. 
- Tap on 'Privacy Center'
- Tap 'Request Your Data'

You will receive an email with a link to download a zip file containing all of your Huawei data. Once downloaded:

- Open the zip file and go to the "data/Motion path detail data & description" folder.
- Extract the file "motion path detail data.json" from the zip file.

Use this file in the --json command line option.  

## How to use the Huawei TCX Converter
You need [`python 3`](https://www.python.org/downloads/) to use this tool.

Download the [Huawei TCX Converter](https://raw.githubusercontent.com/aricooperdavis/Huawei-TCX-Converter/master/Huawei-TCX-Converter.py) and save it as a Python script in the same folder as your HiTrack file.

*Need something simpler? There is [an implementation  of this code online](http://www.hitrackconverter.com/) where you can simply upload your files. Note that this is a closed-source project from an unaffiliated creator.*

### Command Line Arguments Overview
>usage: Huawei-TCX-Converter.py [-h] [-f FILE]
>                     [-s {Walk,Run,Cycle,Swim_Pool,Swim_Open_Water}] [-j JSON]
>                     [-t TAR] [--from_date FROM_DATE]
>                     [--pool_length POOL_LENGTH] [--output_dir OUTPUT_DIR]
>                     [--output_file_prefix OUTPUT_FILE_PREFIX]
>                     [--validate_xml] [--log_level {INFO,DEBUG}]
>

>optional arguments:
>>  -h, --help            show this help message and exit

>>  --log_level {INFO,DEBUG}
>>                        Set the logging level.

>FILE options:
>>  -f FILE, --file FILE  The filename of a single HiTrack file to convert.
>
>> -s {Walk,Run,Cycle,Swim_Pool,Swim_Open_Water}, --sport {Walk,Run,Cycle,Swim_Pool,Swim_Open_Water}
>>                        Force sport for the conversion. Sport will be auto-
>>                        detected when this option is not used.

>JSON options:
>>  -j JSON, --json JSON  The filename of a Huawei Cloud JSON file containing
>>                        the motion path detail data.

>TAR options:
>>  -t TAR, --tar TAR     The filename of an (unencrypted) tarball with HiTrack
>>                        files to convert.

>DATE options:
>>  --from_date FROM_DATE
>>                        Applicable to --json and --tar options only. Only
>>                        convert HiTrack information from the JSON file or from
>>                        HiTrack files in the tarball if the activity started
>>                        on FROM_DATE or later. Format YYYY-MM-DD

>SWIM options:
>>  --pool_length POOL_LENGTH
>>                        The pool length in meters to use for swimming
>>                        activities. If the option is not set, the estimated
>>                        pool length derived from the available speed data in
>>                        the HiTrack file will be used. Note that the available
>>                        speed data has a minimum resolution of 1 dm/s.

>OUTPUT options:
>>  --output_dir OUTPUT_DIR
>>                        The path to the directory to store the output files.
>>                        The default directory is ./output.

>>  --output_file_prefix OUTPUT_FILE_PREFIX
>>                        Adds the strftime representation of this argument as a
>>                        prefix to the generated TCX XML file(s). E.g. use
>>                        %Y-%m-%d- to add human readable year-month-day
>>                        information in the name of the generated TCX file.

>>  --validate_xml        Validate generated TCX XML file(s). NOTE: requires
>>                        xmlschema library and an internet connection to
>>                        retrieve the TCX XSD.
                     
### Usage Examples

#### JSON file conversion example
Use the command below to convert all activities available in the motion path JSON file from the requested Huawei Privacy data that were started
on October, 3rd, 2019 or later. Source HiTrack files and converted TCX files will be generated in folder ./my_output_dir/json 

>python Huawei-TCX-Converter --json "motion path detail data.json" --from_date 2019-10-03 --output_dir my_output_dir/json

#### Single file conversion examples
The example below converts extracted file HiTrack_12345678901212345678912 to HiTrack_12345678901212345678912.tcx in 
the ./output directory

>python Huawei-TCX-Converter --file HiTrack_12345678901212345678912

The next example converts extracted file HiTrack_12345678901212345678912 to HiTrack_12345678901212345678912.tcx in 
the ./my_output_dir directory. The program logging level is set to display debug messages. The converted file is validated against the TCX XSD schema (requires installed xmlschema 
library and an intenet connection). 

>python Huawei-TCX-Converter --file HiTrack_12345678901212345678912 --output_dir my_output_dir --validate_xml --log_level DEBUG

The following example converts an extracted file HiTrack_12345678901212345678912 to HiTrack_12345678901212345678912.tcx 
in the ./output directory and forces the sport to walking. 

>python Huawei-TCX-Converter --file HiTrack_12345678901212345678912 --sport Walk

The next example converts an indoor swimming activity in an extracted file HiTrack_12345678901212345678912 to 
HiTrack_12345678901212345678912.tcx. The length of the pool in meters is specified to have a more accurate swimming data
calculation.  

>python Huawei-TCX-Converter --file HiTrack_12345678901212345678912 --pool_length 25

#### Direct tar file conversion examples
The first example extracts and converts any HiTrack file found in tar file com.huawei.health.tar into the ./output 
directory. The output directory will contain both the extracted HiTrack file and the converted TCX XML file. 

>python Huawei-TCX-Converter --tar com.huawei.health.tar

In the example below, only activities in the com.huawei.health.tar tarball that were started on August 20th, 2019 or 
later will be extracted and converted to the ./output directory.

>python Huawei-TCX-Converter --tar com.huawei.health.tar --from_date 20190820
 

### Illustration
I have copied the `Huawei-TCX-Converter.py` file to the directory containing my HiTrack file (`HiTrack_1551732120000155173259000030001` ). Now I can run the tool as follows:

    python Huawei-TCX-Converter.py --file HiTrack_1551732120000155173259000030001

I've included both the HiTrack file and the resultant TCX file in the Examples folder for you to have a go with. You can also [visualise the data online](https://www.mygpsfiles.com/app/#3gcQ1H3M).

## Comparison
This is an image of the GPS trace from the .tcx file. The command line output above also lists the start time as 2019-03-04 20:42:00, the distance as 1.70km, and the duration as 00:07:49.

![Map of example route](https://raw.githubusercontent.com/aricooperdavis/Huawei-TCX-Converter/master/Examples/Route.PNG)

For comparison, below is the data visable on the Huawei Health App. You can see that the distance is off by about 80m, and the duration off by 1 second, but the GPS trace is spot on.

![Huawei Health App example route](https://raw.githubusercontent.com/aricooperdavis/Huawei-TCX-Converter/master/Examples/Huawei_Health.png)

## Contributing
This is a very early alpha version of this tool, so please help me by making it better! There are some scripts in the Development Tools folder that I find useful for debugging. I'll accept any improvements, but if you're looking for inspiration you could start with this to-do list:
* ~~Remove reliance on using the original filename~~
* ~~Enable changing sport type from running (default) to biking~~
* ~~Read timestamped heart-rate, cadence, and altitude data where available~~
* ~~See if we really need to add the unused data elements (e.g. Calories) to the TCX (edit: we do as there is no minOccurs in the schema)~~
* Add a GUI to make life easier for users who aren't familiar with the command line
* Build for common platforms so that users don't need to install python independently (Android?)
* Work with API's (i.e. Strava/Garmin) for automating tcx upload
* Check that this works for files other than those generated using the Huawei Band 2 Pro:
  * Confirmed working on a [file from a Huawei Watch GT](https://forum.xda-developers.com/smartwatch/huawei-watch/huawei-watch-gt-export-data-health-t3874330#post79042345)
  * Confirmed working on a [file from a Huawei Band 3 Pro](https://github.com/aricooperdavis/Huawei-TCX-Converter/pull/5)
  * Confirmed working on a [file from a Honor Watch Magic](https://forum.xda-developers.com/showpost.php?p=79468704&postcount=35)
  * Confirmed working on a file from a Honor Band 4
* Improve the distance measurement method (currently using [Viscenty's Formulae](https://en.wikipedia.org/wiki/Vincenty%27s_formulae))
* Try and work out what `tp=b-p-m` is
* Add interpolated heart-rate/pace/average speed data to each location element
* ~~Work on splitting data into `Laps`/`Tracks` rather than shoving it all into one~~
* Try to call on an open API to get altitude data for location points that don't have it
* Inspect other files in `com.huawei.health` to see if we can get any more relevant data out of them
  * iOS users may have some success looking at the [SQLite databases included in backups](https://forum.xda-developers.com/showpost.php?p=79445408&postcount=34) from their devices.
