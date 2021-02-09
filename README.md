# afu_ipw_organisationen

## Schema anlegen
```
java -jar /Users/stefan/apps/ili2pg-4.4.5/ili2pg-4.4.5.jar --dbhost localhost --dbport 54321 --dbdatabase edit --dbusr admin --dbpwd admin --nameByTopic --disableValidation  --createEnumTabs --createGeomIdx --createBasketCol --createDatasetCol --createNumChecks --createUnique --createFk --createFkIdx --defaultSrsCode 2056 --models VSADSSMINI_2020_LV95 --modeldir "https://vsa.ch/models" --dbschema dssm_fubar --schemaimport
```

## Organisationen importieren

Daten von https://vsa.ch/models

```
java -jar /Users/stefan/apps/ili2pg-4.4.5/ili2pg-4.4.5.jar --dbhost localhost --dbport 54321 --dbdatabase edit --dbusr admin --dbpwd admin --disableValidation --models SIA405_Base_Abwasser_LV95 --modeldir "https://vsa.ch/models" --dbschema dssm_fubar --dataset organisationen --import vsa_organisationen.xtf
```

## Daten importieren
```
java -jar /Users/stefan/apps/ili2pg-4.4.5/ili2pg-4.4.5.jar --dbhost localhost --dbport 54321 --dbdatabase edit --dbusr admin --dbpwd admin --disableValidation   --models VSADSSMINI_2020_LV95 --modeldir "https://vsa.ch/models" --dbschema dssm_fubar --topics "VSADSSMINI_2020_LV95.VSADSSMini" --import foo_ohne.xtf

java -jar /Users/stefan/apps/ili2pg-4.4.5/ili2pg-4.4.5.jar --dbhost localhost --dbport 54321 --dbdatabase edit --dbusr admin --dbpwd admin --disableValidation   --models VSADSSMINI_2020_LV95 --modeldir "https://vsa.ch/models" --dbschema dssm_fubar --dataset GemeindeFoo --topics "VSADSSMINI_2020_LV95.VSADSSMini" --import foo_alle_org_vorhanden.xtf

```

## Conclusion

1. Wenn die Organisationen nicht mitgeliefert werden und eine referenzierte Organisation in der DB fehlt, bricht Import ab ("dangling reference"). Dies kann umgangen werden, indem man `--createFk` nicht verwendet, dafür `--sqlEnableNull` und `--skipReferenceErrors`. Nicht ganz sicher, ob alle notwendig sind. 
2. Wenn die Organisationen nicht mitgeliefert werden, muss für ilivaliator ein IPW-Organisationsfile vorliegen, damit gegen dieses File getestet werden kann (`--allObjectsAccessible`). Ohne diese Option ist die Validierung positiv, auch wenn eine Organisation dann fehlt.
3. Wenn die Organisationen mitgeliefert werden, kann beim Validieren nicht unterschieden werden, ob die Organisation im Transferfile, das geliefert wird, vorhanden ist, oder in einem vorgehaltenen IPW-Organisationsfile. Wenn der Validator die Organisation findet, ist es ok für ihn. Die mitgelieferten Organisationen werden importiert.

## Validierungsmodell
```
java -jar /Users/stefan/apps/ili2c-5.1.4/ili2c.jar --modeldir "https://vsa.ch/models" VSADSSMINI_CHECK_IPW_20210209.ili
```

```
java -jar /Users/stefan/apps/ilivalidator-1.11.9/ilivalidator-1.11.9.jar --modeldir "https://vsa.ch/models;." --config ipw_check.toml foo.xtf
```

## Datenbank
```
docker volume prune

docker-compose build
docker-compose down
docker-compose up
```