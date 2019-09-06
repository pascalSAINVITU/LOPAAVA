# LOPAAVA
Last Or Popular AA Versions Available

## to add your AA as a new entry or to update with a new version:
* 'register' = <aa name> 
* 'address' = <aa address> . 
  
## to give your opinion on a version of an AA (you need to be attested by the real name attestor)
* '(dis)like' = <aa name>
* 'version' = <version>
  

{ 
	/*
	Check in explorer: https://testnetexplorer.obyte.org/#uTulONqpLeCW8dkAAGHl51Fy9GjAX1Ucu3MmtkV9toE=
    Agent address: 75KDQ5KTLGH5R552RG6JJQGYEMZAU4GG
	*/
	bounce_fees: { // bounce fees declarations
		base: 10000 
	},
	init: "{ 
		// forever constantes
			$INSTRUCTIONS = "Please use 'register' = <aa name> + 'address' = <aa address> to add your AA as a new entry or to update with a new version. Please use '(dis)like' = <aa name> + 'version' = <version> to give your opinion on a version of an AA (you need to be attested by the real name attestor).!";

			$AA_OWNER = "O7NYCFUL5XIJTYE3O4MKGMGMTN6ATQAJ";
			$ATTESTATOR = '35IT3ZYKEPQSCXG2F7A7KWNRAD3SZXN4';
			
		// see all possible inputs here:
			$i = trigger.data;
			$register = $i.register;                     $aa_address = $i.address;
			$like = $i.like; $dislike = $i.dislike;      $aa_version = $i.version;
			$need_help = $i.help otherwise $i.h;
			
		// define var that are use in each use cases
			$name = $i.register otherwise $i.like otherwise $i.dislike otherwise bounce ($INSTRUCTIONS);
			$key = $name||"_";
			$popular_version = var[$key||"popular_version"];
	}",
	messages: { // template for the AA's response transaction
		cases: [ // selection of your AA functionnality/use cases
			{ if: "{ !!$i.regiter }",
				init: "{ 
					if (!!var[$key||"owner"])
						if (var[$key||"owner"] != trigger.address)
							bounce ("You are not the owner !");
					if (!$aa_address) bounce ("Please specify 'address' !");		
					$version = var[$key||"last_version"] + 1 otherwise 1;
				}",
				messages: [
					{ 
						app: "state", state: "{
						// for each AA we want to keep:
							var[$key||"owner"] = trigger.address; // only user allow to update the AA
							var[$key||"last_version"] = $version; 
							var[$key||"popular_version"] = $popular_version otherwise $version;
						// for each version we want to keep
							var[$key||$version||"_address"] = $aa_address;
							var[$key||$version||"_likes"] = 0;
							var[$key||$version||"_dislikes"] = 0;
							var[$key||$version||"_date"] = timestamp_to_string(timestamp);
						// response
							response['message'] = "New version of your aa has been registered ^^";
						}"
					}
				]
			},
			{ if: "{ !!$i.like or !!$i.dislike}",
				init: "{ 
					if (attestation[[attestors=$ATTESTATOR, address=trigger.address, ifnone=false]] == false)
						bounce ("To like or dislike you must be attested by "||$ATTESTATOR);
					$version = $aa_version otherwise bounce ("Please specify 'version'");
					if (!var[$key||$version||"_address"])
						bounce ($key||$version||" do not exist !");
				}",
				messages: [
					{ // payment application (one per asset)
						app: 'payment', payload: { asset: "base", outputs: [     
							{ address: "{trigger.address}",  amount: "{ trigger.output[[asset=base]]-1000 }" },
						] }
					},
					{ 
						app: "state", state: "{
							// proceed voting
								if ($b_want_to_like) var[$key||$version||"_likes"] +=1;
								if ($b_want_to_dislike) var[$key||$version||"_dislikes"] -=1;
							// check if it became the most popular version
								if (var[$key||$version||"_likes"] > var[$key||$popular_version||"_likes"])
									var[$key||"popular_version"] = var[$key||$version];
							response['message'] = "You vote has been taken into account ^^";
						}"
					}
				]
			},
			{ // default case
				messages: [
					{ 
						app: "state", state: "{
							if ($need_help) bounce ($INSTRUCTIONS);
						}"
					}
				]
			},
		]
	}
}
