
### Run with proxyHost

./gradlew -Dhttps.proxyHost=iP -Dhttps.proxyPort=3128 -Dhttp.proxyHost=iP -Dhttp.proxyPort=3128 currentVersion



### Versioning
` Increment major
./gradlew release -Prelease.versionIncrementer=incrementMajor

`Increment minor 
./gradlew cV
2.0.1-SNAPSHOT

`./gradlew marNextVersion -Prelease.incrementer=incrementMinor
./gradlew cV
2.1.0-SNAPSHOT


` Increment bug fix

` ./gradlew cV
2.0.0-SNAPSHOT

` ./gradlew markNextVersion

` ./gradlew cV
2.0.1-SNAPSHOT



` Overriding version

./gradlew release -Prelease.forceVersion=3.0.0



` Force SNAPSHOT version

./gradlew build publish -Prelease.forceSnapshot

` Set specific version

./gradlew markNextVersion -Prelease.version=2.0.0

