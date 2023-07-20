# ci-lab
laboratorio de integración continua.



# GitHub Actions

- Configura una action importando el plugin "Java with Maven", tal como lo mostró el profesor
- Modifica el archivo `maven-publish.yml` dejándolo así:

```
name: Maven Package

on: push

jobs:
  build:

    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
    - uses: actions/checkout@v3
    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'temurin'
        server-id: github # Value of the distributionManagement/repository/id field of the pom.xml
        settings-path: ${{ github.workspace }} # location for the settings.xml file

    - name: Build with Maven
      run: mvn -B package --file pom.xml # or verify
```
- Haz un push en tu repo para gatillar el action (modifica un archivo por ejemplo si estás usando GitHub directamente)
- Luego reemplaza `package` por `verify`
- Modifica los tests para que fallen y haz un push
- Modifica los tests para que pasen y haz un push
- Crea un token clasic en tu configuración personal, asegurate que tenga permisos para write/delete packages. (El token clasic se crea en `Settings/Developer Settings`)
- Crea los secrets USER y TOKEN en el repo
- Revisa en contenido de settings.xml en la carpeta .m2. Modifica la linea 20 para que haga referencia a tu repo.
- Modifica el archivo `maven-publish.yml` y déjalo así:
```

# This workflow will build a package using Maven and then publish it to GitHub packages when a release is created
# For more information see: https://github.com/actions/setup-java/blob/main/docs/advanced-usage.md#apache-maven-with-a-settings-path

name: Maven Package

on: push

jobs:
  build:

    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
    - uses: actions/checkout@v3
    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'temurin'
        server-id: github # Value of the distributionManagement/repository/id field of the pom.xml
        settings-path: ${{ github.workspace }} # location for the settings.xml file

    - name: Build with Maven
      run: mvn -B verify --file pom.xml
      
    - name: Create staging directory
      run: mkdir staging && cp target/*.jar staging
    
    - name: Store artifact
      uses: actions/upload-artifact@v3
      with:
        name: Package
        path: staging

    - name: Publish to GitHub Packages Apache Maven
      run: mvn deploy -s .m2/settings.xml
      env:
         GITHUB_TOKEN: ${{ github.token }}

```

IMPORTANTE: recuerda modificar las referencias a los repos en los archivos maven
