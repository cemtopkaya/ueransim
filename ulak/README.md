### Ortak Mongo Ayarları
- Kullanıcı Listeleme
- Kullanıcı Oluşturma
- DB Listesini Çek

#### Kullanıcıları Listele

```sh
clear
export MONGO_IP="10.100.100.3"
export MONGO_USERNAME="cnrusr"
export MONGO_PASS="P5vKG6vE"
echo '
use admin
db.getUsers()

const dbs = db.adminCommand("listDatabases").databases
dbs.forEach(dbInfo => {
    print("Veritabanı: " + dbInfo.name);
    try {
        const currentDb = db.getSiblingDB(dbInfo.name);
        // Bu veritabanındaki kullanıcıları listele
        const users = currentDb.getUsers();
        if (users.length > 0) {
            print("Kullanıcılar:");
            users.forEach(user => {
                print(" - " + user.user + " (Rol: " + user.roles.map(r => r.role).join(", ") + ")");
            });
        } else {
            print("Bu veritabanında kullanıcı yok.");
        }
    } catch (e) {
        print("Hata: " + e);
    }
    print("--------------------");
});' | mongosh --host $MONGO_IP --port 27017 -u $MONGO_USERNAME -p $MONGO_PASS --authenticationDatabase "admin"
```

#### Kullanıcıyı Veritabanına Yetkilendir

```sh
clear
export MONGO_IP="10.100.100.3"
export MONGO_USERNAME="cnrusr"
export MONGO_PASS="P5vKG6vE"
echo '
db.grantRolesToUser("cnrusr", [ { role: "readWrite", db: "cinarnrftest" } ])

use admin
// admin DBde tanımlı tüm kullanıcılar
db.getUsers()

// cnrusr Kullanıcısının bilgileri
db.getUser("cnrusr")

// cnrusr Kullanıcısının tüm bilgiler (yetkili olduğu DB ve rolleri)
db.runCommand({ usersInfo: { user: "cnrusr", db: "admin" }, showPrivileges: true })
' | mongosh --host $MONGO_IP --port 27017 -u $MONGO_USERNAME -p $MONGO_PASS --authenticationDatabase "admin"
```


#### Kullanıcıları Oluştur
```sh
clear
export MONGO_IP="10.100.100.3"
export MONGO_USERNAME="cnrusr"
export MONGO_PASS="P5vKG6vE"
echo '
    db.createUser(
        {
          user: "cnrusr",
          pwd: "P5vKG6vE",
          roles: [
            {
              role: "root",
              db: "admin"
            }
          ]
        }
    );' | mongosh --host $MONGO_IP --port 27017 -u $MONGO_USERNAME -p $MONGO_PASS --authenticationDatabase "admin"
```

#### DB Listesini Çek
```sh
clear
export MONGO_IP="10.100.100.3"
export MONGO_USERNAME="cnrusr"
export MONGO_PASS="P5vKG6vE"
echo '
// Mevcut veritabanlarının listesini görüntüleyin
const databases = db.adminCommand("listDatabases").databases;
print("------ DBs -------")
print(databases)

databases.forEach(dbInfo => {
    print("Veritabanı: " + dbInfo.name);
    // Veritabanına geçiş yap (doğrudan JavaScript kullanımıyla)
    const currentDb = db.getSiblingDB(dbInfo.name); 
    // Koleksiyonları listele
    const collections = currentDb.getCollectionNames(); 
    print("Koleksiyonlar: ");
    collections.forEach(collection => {
        print(" - " + collection);
    });
});
' | mongosh --host $MONGO_IP --port 27017 -u $MONGO_USERNAME -p $MONGO_PASS --authenticationDatabase "admin"
```

### NRF Mongo Ayarları
```sh
clear
# create db and collections
echo '
    db.getSiblingDB("cinarnrftest").createCollection("cinarnfcollection", { capped : true, size : 6142800, max : 10000 } );
    db.getSiblingDB("cinarnrftest").createCollection("cinarsubscollection", { capped : true, size : 6142800, max : 10000 } );
    db.getSiblingDB("cinarnrftest").createCollection("cinarnfstatecollection", { capped : true, size : 6142800, max : 10000 } );' | mongosh --host $MONGO_IP --port 27017 -u $MONGO_USERNAME -p $MONGO_PASS --authenticationDatabase "admin"
```

### AMF Mongo Ayarları
```sh
clear
# amf'in settings.json dosyasındaki Mongo DB adıyla ile aynı olmalı
export MONGO_AMF_DB_NAME="amf"
export MONGO_IP="10.100.100.3"
export MONGO_USERNAME="cnrusr"
export MONGO_PASS="P5vKG6vE"
export NRF_IP=${NRF_IP_ADDRESS-"10.100.100.5"}
export AMF_HOST_IP="10.100.100.6"

# _id:0 silinsin ki tekrar oluşturulsun

echo '
db.getSiblingDB("AmfDB").getCollection("AmfList").deleteOne({ _id: "0" })
' | mongosh --host $MONGO_IP --port 27017 -u $MONGO_USERNAME -p $MONGO_PASS --authenticationDatabase "admin"

# Tekrar oluşturalım
export JSON=`cat << EOF
{
        "_id": "0",
        "amfKpi" : {
            "maxNOfRegisteredSubscribers" : 588,
            "meanNofRegisteredSubscribers" : 0,
            "nOf5gPagingProcedures" : 4,
            "nOf5gToEpsHoAttempt" : 0,
            "nOf5gToEpsHoFailCause" : {},
            "nOf5gToEpsHoSucc" : 0,
            "nOfEmergencyRegistrationRequestsPerSNSSAI" : {
                " " : 4
            },
            "nOfEpsTo5gHoAttempt" : 0,
            "nOfEpsTo5gHoFailCause" : {},
            "nOfEpsTo5gHoSucc" : 0,
            "nOfInitialRegistrationRequestsPerSNSSAI" : {
                " " : 51917,
                "1" : 116093,
                "2" : 3
            },
            "nOfMobilityRegistrationUpdateRequestsPerSNSSAI" : {
                " " : 4,
                "1" : 4
            },
            "nOfPduSessionFailPerCause" : {},
            "nOfPduSessionFailPerSNSSAI" : {},
            "nOfPduSessionReqPerSNSSAI" : {},
            "nOfPeriodicRegistrationUpdateRequestsPerSNSSAI" : {
                " " : 2
            },
            "nOfRegRequestsforSMSoverNAS" : 5,
            "nOfSucc5gPagingProcedures" : 4,
            "nOfSuccEmergencyRegistrationsPerSNSSAI" : {
                " " : 4
            },
            "nOfSuccInitialRegistrationsPerSNSSAI" : {
                " " : 39,
                "1" : 83922
            },
            "nOfSuccMobilityRegistrationUpdatesPerSNSSAI" : {
                " " : 4,
                "1" : 4
            },
            "nOfSuccPeriodicRegistrationUpdatesPerSNSSAI" : {
                " " : 1
            },
            "nOfSuccRegistrationsAllowedforSMSoverNAS" : 5,
            "nOfSuccUEConfigUpdate" : 3,
            "nOfTotalRegistrationRequests" : 168027,
            "nOfTotalSuccRegistration" : 83974,
            "nOfUEConfigUpdate" : 8,
            "registeredSubscribersOfNwSlice" : {}
        },
        "emergencyConfigurationData" : {
            "dnn" : "dnn.emergency.com",
            "emergencySmfUuid" : "",
            "snssai" : {
                "sst" : 1
            },
            "supportEmergencyRegForUnauthenticatedSUPIs" : true
        },
        "generalParameters" : {
            "allowPagingAtOverload" : false,
            "discoverNFsInSamePlmnAtStartup" : false,
            "gtpInterfaceAmfInfo" : {
                "amfName" : "AMF_GTP_FQDN",
                "ipv4EndpointAddress" : [
                    "$AMF_HOST_IP"
                ],
                "ipv6EndpointAddress" : []
            },
            "gtpInterfaceMmeInfo" : {
                "ipv4EndpointAddress" : [
                    "$AMF_HOST_IP"
                ],
                "ipv6EndpointAddress" : [],
                "mmeName" : "MME"
            },
            "kpiSamplingPeriod" : 3600,
            "nrfCallbackPort" : "6287",
            "pagingRetransmissionCount" : 4,
            "pagingRetransmissionTimerValue" : 6,
            "prometheusServerPort" : "6224",
            "sbiCallbackPort" : "6286",
            "udsfDeployment" : false
        },
        "ladnList" : [
            {
                "ladnDnn" : "dnn.ladn0.com",
                "ladnServiceArea" : [
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000001"
                    },
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000002"
                    }
                ]
            }
        ],
        "liConfigurations" : {
            "liAdmfServiceEndPoint" : {
                "ipv4Address" : "$AMF_HOST_IP",
                "port" : 6294
            },
            "liMdfServiceEndPoint" : {
                "ipv4Address" : "$AMF_HOST_IP",
                "port" : 6295
            },
            "liNeServiceEndPoint" : {
                "ipv4Address" : "$AMF_HOST_IP",
                "port" : 6292
            }
        },
        "nfProfile" : {
            "allowedNfTypes" : [
                "SMF",
                "PCF",
                "UDM",
                "AUSF",
                "NSSF",
                "AMF",
                "SMSF"
            ],
            "allowedNssais" : [
                {
                    "sst" : 1
                }
            ],
            "allowedPlmns" : [
                {
                    "mcc" : "001",
                    "mnc" : "002"
                }
            ],
            "amfInfo" : {
                "amfRegionId" : "01",
                "amfSetId" : "002",
                "backupInfoAmfFailure" : [],
                "backupInfoAmfRemoval" : [
                    {
                        "amfId" : "010081",
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        }
                    }
                ],
                "guamiList" : [
                    {
                        "amfId" : "010080",
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        }
                    }
                ],
                "n2InterfaceAmfInfo" : {
                    "amfName" : "AmfSto1",
                    "ipv4EndpointAddress" : [
                        "$AMF_HOST_IP"
                    ]
                },
                "taiList" : [
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000000"
                    },
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000001"
                    },
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000002"
                    },
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000004"
                    },
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000005"
                    },
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000006"
                    },
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000007"
                    },
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000008"
                    },
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tac" : "000009"
                    }
                ],
                "taiRangeList" : [
                    {
                        "plmnId" : {
                            "mcc" : "001",
                            "mnc" : "002"
                        },
                        "tacRangeList" : [
                            {
                                "end" : "000009",
                                "start" : "000000"
                            }
                        ]
                    }
                ]
            },
            "capacity" : 15,
            "fqdn" : "",
            "ipv4Addresses" : [
                "$AMF_HOST_IP"
            ],
            "ipv6Addresses" : [],
            "load" : 0,
            "locality" : "geographic location",
            "nfInstanceId" : "529ba60e-0e57-4f73-804f-1548bf76cb2f",
            "nfServices" : [
                {
                    "defaultNotificationSubscriptions" : [
                        {
                            "callbackUri" : "http://$AMF_HOST_IP:6286/namf-comm/v1/onN1N2MessageNotify/n1MessageNotify",
                            "n1MessageClass" : "5GMM",
                            "notificationType" : "N1_MESSAGES"
                        }
                    ],
                    "fqdn" : "",
                    "ipEndPoints" : [
                        {
                            "ipv4Address" : "$AMF_HOST_IP",
                            "port" : 6210,
                            "transport" : "TCP"
                        }
                    ],
                    "nfServiceStatus" : "REGISTERED",
                    "scheme" : "http",
                    "serviceInstanceId" : "1",
                    "serviceName" : "namf-comm",
                    "versions" : [
                        {
                            "apiFullVersion" : "1.0.2",
                            "apiVersionInUri" : "v1"
                        }
                    ]
                },
                {
                    "fqdn" : "",
                    "ipEndPoints" : [
                        {
                            "ipv4Address" : "$AMF_HOST_IP",
                            "port" : 6211,
                            "transport" : "TCP"
                        }
                    ],
                    "nfServiceStatus" : "REGISTERED",
                    "scheme" : "http",
                    "serviceInstanceId" : "2",
                    "serviceName" : "namf-evts",
                    "versions" : [
                        {
                            "apiFullVersion" : "1.0.2",
                            "apiVersionInUri" : "v1"
                        }
                    ]
                },
                {
                    "fqdn" : "",
                    "ipEndPoints" : [
                        {
                            "ipv4Address" : "$AMF_HOST_IP",
                            "port" : 6212,
                            "transport" : "TCP"
                        }
                    ],
                    "nfServiceStatus" : "REGISTERED",
                    "scheme" : "http",
                    "serviceInstanceId" : "3",
                    "serviceName" : "namf-mt",
                    "versions" : [
                        {
                            "apiFullVersion" : "1.0.2",
                            "apiVersionInUri" : "v1"
                        }
                    ]
                },
                {
                    "fqdn" : "",
                    "ipEndPoints" : [
                        {
                            "ipv4Address" : "$AMF_HOST_IP",
                            "port" : 6213,
                            "transport" : "TCP"
                        }
                    ],
                    "nfServiceStatus" : "REGISTERED",
                    "scheme" : "http",
                    "serviceInstanceId" : "4",
                    "serviceName" : "namf-loc",
                    "versions" : [
                        {
                            "apiFullVersion" : "1.0.2",
                            "apiVersionInUri" : "v1"
                        }
                    ]
                }
            ],
            "nfStatus" : "REGISTERED",
            "nfType" : "AMF",
            "perPlmnSnssaiList" : [
                {
                    "plmnId" : {
                        "mcc" : "001",
                        "mnc" : "002"
                    },
                    "sNssaiList" : [
                        {
                            "sst" : 1
                        }
                    ]
                }
            ],
            "plmnList" : [
                {
                    "mcc" : "001",
                    "mnc" : "002"
                }
            ],
            "priority" : 0,
            "sNssais" : [
                {
                    "sst" : 1
                }
            ]
        },
        "nrfServicesConfigurations" : {
            "accessTokenServiceEndPoint" : {
                "ipv4Address" : "$NRF_IP",
                "port" : 8007
            },
            "nfDiscoveryServiceEndPoint" : {
                "ipv4Address" : "$NRF_IP",
                "port" : 8006
            },
            "nfManagementServiceEndPoint" : {
                "ipv4Address" : "$NRF_IP",
                "port" : 8001
            },
            "scheme" : "http"
        },
        "overloadControlParameters" : {
            "n2OverloadLimit" : 50000,
            "nasCongestionLimitAfterOverload_DNN" : 100000,
            "nasCongestionLimitAfterOverload_General" : 100000,
            "nasCongestionLimitAfterOverload_perSNSSAI" : [
                {
                    "nasCongestionLimitAfterOverload_SNSSAI" : 100000,
                    "snssai" : {
                        "sst" : 1
                    }
                }
            ]
        },
        "sctpConfigurations" : {
            "sctpFlags" : {
                "o_nonblock" : true,
                "sctp_nodelay" : true,
                "sctp_peer_addr_params" : true,
                "sctp_rtoinfo" : true,
                "so_reuseaddr" : true,
                "so_reuseport" : true
            },
            "sctpOptions" : {
                "ngap_port_number" : 38412,
                "sctp_heartbeat_interval" : 5000,
                "sctp_max_attempts" : 5,
                "sctp_max_initial_timeout" : 5000,
                "sctp_max_num_of_istreams" : 64,
                "sctp_max_num_of_ostreams" : 8,
                "sctp_number_of_listeners" : 4,
                "sctp_poll_timeout" : 3000,
                "sctp_rto_initial" : 3000,
                "sctp_rto_max" : 5000,
                "sctp_rto_min" : 1000,
                "sctp_time_to_live" : 6000
            }
        }
    }
EOF`

echo '
    db.getSiblingDB("'$MONGO_AMF_DB_NAME'").createCollection("AmfList", { capped : true, size : 6142800, max : 10000 } );
    db.getSiblingDB("'$MONGO_AMF_DB_NAME'").getCollection("AmfList").insertOne('$JSON')' | mongosh --host $MONGO_IP --port 27017 -u $MONGO_USERNAME -p $MONGO_PASS --authenticationDatabase "admin"
```