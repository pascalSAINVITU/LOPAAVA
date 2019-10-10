# LOPAAVA
Last Or Popular AA Versions Available
If you want to use a secondary AA which do not need to be trustless you can keep your AA up to date, by using LOPAAVA to get the last version or the most popular version of a secondary AA.

Full tutorial here: https://medium.com/autonomous-agents-powered-by-obyte/last-or-popular-version-of-an-autonomous-agent-aa-powered-by-obyte-7a9e34082b9e

## to add your AA as a new entry or to update with a new version:
* 'register' = < aa name >
* 'address' = < aa address >
  
## to give your opinion on a version of an AA (you need to be attested by the real name attestor)
* '(dis)like' = < aa name >
* 'version' = < version >

## to use LOPAAVA from another AA:
$LOPAAVA = "K6XFY4PODYMWPUELDOYG6UOJ3GQLMDMT";
* $needed_aa_address = var[$LOPAAVA][<AA_NAME>_popular_version];
* $needed_aa_address = var[$LOPAAVA][<AA_NAME>_last_version];
