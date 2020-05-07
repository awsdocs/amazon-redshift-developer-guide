# enable\_vacuum\_boost<a name="r_enable_vacuum_boost"></a>

## Values \(default in bold\)<a name="r_enable_vacuum_boost-values"></a>

**false**, true

## Description<a name="description"></a>

Specifies whether to enable the vacuum boost option for all VACUUM commands run in a session\. If `enable_vacuum_boost` is `true`, Amazon Redshift runs all VACUUM commands in the session with the BOOST option\. If `enable_vacuum_boost` is `false`, Amazon Redshift doesn't run with the BOOST option by default\. For more information about the BOOST option, see [VACUUM](r_VACUUM_command.md)\. 