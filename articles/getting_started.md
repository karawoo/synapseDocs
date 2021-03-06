---
title: "Getting Started with Synapse"
layout: article
excerpt: A getting started guide for new users who are interested in learning about Synapse.
category: intro
order: 1
---

<script src='/assets/javascripts/words.js'></script>

<style>
#webTab {
    width: 90%;
}
</style>

# Get Started with Synapse

This getting started guide is for new users who are interested in learning about Synapse. By following this guide, you will learn fundamental Synapse features by performing some simple tasks. You’ll learn how to:

* Create your own project and add content to Synapse
* Understand and use some of the many Synapse features
* Install one of the Synapse clients (R, Python or command line)
* Incorporate Synapse in your workflows to read shared content and upload analysis results

## What is Synapse?
Synapse is an open source software platform that data scientists can use to carry out, track, and communicate their research in real time. Synapse enables co-location of scientific content (data, code, results) and narrative descriptions of that work. Synapse has seeded a growing number of living [research projects](https://www.synapse.org/#!StandaloneWiki:ResearchCommunities) and [resources](https://www.synapse.org/#!StandaloneWiki:OpenResearchProjects) including [Sage/DREAM Challenges](http://dreamchallenges.org/).

[Sage Bionetworks](http://www.sagebase.org){:target="_blank"} provides Synapse services free of charge to the scientific community through generous support from various [funding sources](/articles/faq.html#how-is-synapse-funded){:target='_blank'}. Synapse hosts many living [research projects](https://www.synapse.org/#!StandaloneWiki:ResearchCommunities) and [resources](https://www.synapse.org/#!StandaloneWiki:OpenResearchProjects) including [Sage/DREAM Challenges](http://dreamchallenges.org/).

With Synapse, you can:
=======
* create your own personal project workspaces
* populate your projects with files and tables such as data, code, and results as well as the provenance relationships that tie these resources together
* richly annotate files and tables to increase discoverability and aid in programmatic querying of these resources
* provide a project narrative which lives along side the scientific artifacts of your work, via the Synapse `Wiki` tools
* create a [DOI](http://en.wikipedia.org/wiki/Digital_object_identifier){:target="_blank"} for any resource for easy citation of your work
* share your work with other Synapse users, teams of users, or make your work public
* discuss with researchers in a project using discussion forums

<br/>
Synapse operates under a complete [governance process](/articles/governance.html) that includes well-documented [Terms and Conditions of Use](https://s3.amazonaws.com/static.synapse.org/governance/SageBionetworksSynapseTermsandConditionsofUse.pdf?v=4){:target="_blank"}, guidelines and operating procedures, privacy enhancing technologies, as well as the right of audit and external reviews.

# Becoming a Synapse Registered User

Anyone can browse public content on the Synapse web site, but in order to download and create content you will need to register for an account using an email address. You will receive an email message for verification to complete the registration process.

<a href="https://www.synapse.org/register" class="btn btn-primary">Register</a>{:target="_blank"}

### Becoming a Certified User

Since Synapse can store human subject data that has sharing and use restrictions, before you can create content you will need to become a Certified User and take a quiz about what kinds of items can be shared in Synapse. To start this process:

<a href="https://www.synapse.org/#!Quiz:Certification" class="btn btn-primary">Become a Certified User</a>{:target="_blank"}

Explore our [accounts, certification and profile validation](/articles/accounts_certified_users_and_profile_validation.html) page to find out more information on the different levels of users.  

# Installing Synapse Clients
<img style="float:right;" src="/assets/images/synapse_apis.png">

Synapse is built on a number of RESTful web APIs that allow users to interact with the system via a number of _clients_. One of these _clients_ is the web client, i.e. the website [www.synapse.org](https://www.synapse.org/). Synapse also provides three programmatic clients (R, Python, and Command Line). Content can be uploaded, downloaded, annotated, and queried from any of these interfaces. In the getting started guide we will run through examples using all three programmatic interfaces.  At any point you can pick the language you would like to see examples in by clicking the corresponding tab at the bottom of every example.  Unless otherwise noted the examples are can be typed into the respective environment.  That is a shell prompt for the command line examples, a Python session such as an ipython notbook of script, and an R session for the R examples.

{% tabs %}
{% tab Command %}

In a terminal window type the following command and hit enter. (For alternative methods of installation see the Python client installation instructions.)

{% highlight bash %}
pip install synapseclient
{% endhighlight %}
{% endtab %}


{% tab Python %}
{% highlight python %}
pip install synapseclient
{% endhighlight %}
{% endtab %}

{% tab R %}
{% highlight r %}
install.packages("synapser", repos=c("https://sage-bionetworks.github.io/ran", "http://cran.fhcrc.org"))
{%endhighlight %}
{% endtab %}

{% tab Web %}
Navigate to [www.synapse.org](https://www.synapse.org) - nothing to install
{% endtab %}

{% endtabs %}


## Logging into Synapse

Synapse credentials are required to use the programmatic clients. Register to create an account, and even if you login with a Google account, make sure you go through the extra step of creating a Synapse username and password.

At the command line you can login by specifying your Synapse username and password.

The login credentials can be specified for every Synapse client session, but this is not the recommended practice as your password will be visible. Instead, by passing the `rememberMe` parameter you can cache your credentials for use in future Synapse client sessions.

The full list of possible login parameters for the Python client can be found in the [Python Docs](https://python-docs.synapse.org/build/html/Client.html#login) and for the R client in the [R Docs](https://r-docs.synapse.org/articles/synapser.html#connecting-to-synapse). 

To login with your username/email and password:

{% tabs %}

{% tab Command %}
{% highlight bash %}
# by passing --rememberMe the username/password will not need to specified on subsequent calls to Synapse.
synapse login -u me@example.com -p secret --rememberMe
{% endhighlight %}
{% endtab %}


{% tab Python %}
{% highlight python %}
import synapseclient
# If you have your config file set up you can run:
syn = synapseclient.login()
# Otherwise, pass in your username and password:
syn = synapseclient.login(email='me@example.com', password='secret', rememberMe=True)
{% endhighlight %}
{% endtab %}

{% tab R %}
{% highlight r %}
library(synapser)
# If you have your config file set up you can run:
synLogin()
# Otherwise, pass in your username and password:
synLogin(username='me@example.com', password='secret', rememberMe=TRUE)
{% endhighlight %}
{% endtab %}

{% endtabs %}


### Using a Config File

You can store your credentials in your home directory in a file called `.synapseConfig` (note the period at the beginning of the file which makes this a hidden, system file on Linux-like OS's. The format is as such:

```
[authentication]
username: me@example.com
password: secret
```

# Project and Data Management on Synapse

<img style="float: right" src="/assets/images/project_1.png">

Once you have a Synapse account and have installed a client (or navigated to the Synapse website) you can start adding content. All Synapse content is organized in user-created `Projects`, an organizational unit in which you can collaboratively access and share `Wikis` (narratives), `Files` (a distributed file system to store data, code, and results from your work), and `Tables` (web-accessible, sharable, and queryable data where columns can have a user-specified structured schema). Each `Project` also contains a project-specific `Discussion Forum`.

By default, your newly created `Project` is private; you are the only person who can access it and any content you include in it. To invite others to view or edit your `Project`, click on the Share icon in the upper right hand portion of the screen. For more information on Sharing, please see the [Content Controls](/articles/access_controls.html) article.

As an exercise we are going to create an example `Project` to store some cell line analysis.
Decide on a unique name for your `Project`. Since `Project` names must be unique in Synapse, let me suggest a project name for you:

**<span id='random_proj_name'>Foo</span>**<br/>

Use this project name in the example scripts below.

<script type="text/javascript">
var chance = window.Chance.Chance();
var myadj = chance.capitalize(chance.pickone(adjectives));
var mynoun = chance.capitalize(chance.pickone(nouns));
var new_random_string = "Project ".concat(myadj, " ", mynoun);

var randomProjNameElement = document.getElementById("random_proj_name");
randomProjNameElement.innerHTML = new_random_string;
</script>

{% tabs %}
{% tab Command %}
	{% highlight bash %}
synapse create Project -name "My uniquely named project"
	{% endhighlight %}
{% endtab %}


	{% tab Java %}
	{% highlight java %}
import org.sagebionetworks.client.*;
import org.sagebionetworks.repo.model.*;

public static String PROD_REPO_URL = "https://repo-prod.prod.sagebase.org/repo/v1";
public static String PROD_AUTH_URL = "https://repo-prod.prod.sagebase.org/auth/v1";
public static String PROD_FILE_URL = "https://repo-prod.prod.sagebase.org/file/v1";

SynapseClient synapseClient = new SynapseClientImpl();
synapseClient.setSessionToken(sessionToken);
synapseClient.setRepositoryEndpoint(PROD_REPO_URL);
synapseClient.setAuthEndpoint(PROD_AUTH_URL);
synapseClient.setFileEndpoint(PROD_FILE_URL);

Project myProject = new Project();
myProject.setName("My uniquely named project");
myProject = synapseClient.createEntity(myProject);
System.out.println("Created a project with Synapse id " + myProject.getId());
	{%endhighlight %}
	{% endtab %}

    {% tab Python %}
	{% highlight python %}
import synapseclient
from synapseclient import Wiki, File, Project, Folder
syn = synapseclient.login()
myProject = syn.store(Project(name="My uniquely named project"))
print 'Created project with synapse id: %s' % myProject.id
	{% endhighlight %}
	{% endtab %}

    {% tab R %}
	{% highlight r %}
library(synapser)
synLogin()
myProject <- synStore(Project(name="My uniquely named project"))
print(paste('Created a project with Synapse id', myProject$properties$id, sep = ' '))
	{%endhighlight %}
	{% endtab %}

    {% tab Web %}
Go to your [profile Page](https://www.synapse.org/#!Profile:v) and click the **Create Project** button
    {% endtab %}
{% endtabs %}
<br>

By default, your newly created `Project` is private; you are the only person who can access it and any content you include in it.  Later on we will share your created `Project` with other users.

Objects like `Files`, `Folders`, `Projects` created in Synapse are assigned unique identifiers which are used for unique reference (a Synapse ID) with the format `syn12345678`. For example, your newly created `Project` will be assigned a Synapse ID.


{% include note.html content= " Synapse Ids are used to uniquely identify Files, Folders, Projects and Tables in Synapse." %}

You can view what you have created in Synapse on the web with:

{% tabs %}
{% tab Command %}
	{% highlight bash %}
synapse onweb syn123  #where syn123 is replaced with the synapse Id of your project
	{% endhighlight %}
{% endtab %}

    {% tab Java %}
	{% highlight java %}
java.awt.Desktop.getDesktop().browse("https://www.synapse.org/#!Synapse:"+myProject.getId());
	{%endhighlight %}
	{% endtab %}

    {% tab Python %}
	{% highlight python %}
syn.onweb(myProject)
	{% endhighlight %}
	{% endtab %}

    {% tab R %}
	{% highlight r %}
synOnweb(myProject)
	{%endhighlight %}
	{% endtab %}

    {% tab Web %}
Go to your [profile Page](https://www.synapse.org/#!Profile:v) and click the project name in the Projects listing {% endtab %}
{% endtabs %}

# Adding a Wiki to your Project

The `Wiki` tab in a `Project` provides a space for you to build narrative content to describe your research. These `Wikis` can also be nested as subpages to build up a hierarchy of content within your `Project` as well as be attached to specific `Files` and `Folders` in your `Project`.  Examples of content that you may want to include are project descriptions, specific aims, progress updates of data generation or analysis, analysis results (either in prose or via markdown-based notebooks such as [knitr](http://yihui.name/knitr/){:target="_blank"} or [IPython notebook](http://ipython.org/notebook.html){:target="_blank"}), or web-accessible publication-like summaries of your research.

`Wiki` pages can contain highly customized content including, but not limited to images, tables, code blocks, LaTeX formatted equations, and scholarly references. Synapse-specific widgets also allow users to embed dynamic content based on other resources stored in Synapse (e.g., Entity List, User/Team badge, Query Table, or Provenance Graph).

See the [Wiki](/articles/wikis.html) user guide for more information and examples.

Here we will create a small `Wiki`:

{% tabs %}
{% tab Command %}
	{% highlight bash %}
The command line client does not support the creation of wiki content.
We suggest using (to get to the webpage of the project)
synapse onweb syn###
where syn### is the Synapse Id of your created project.  Then editing the wiki using the web client.
	{% endhighlight %}
{% endtab %}

    {% tab Java %}
	{% highlight java %}
import org.sagebionetworks.repo.model.wiki.WikiPage;

WikiPage page = new WikiPage();
page.setTitle("Data Summary");
page.setMarkdown("* Cell growth look normally distributed\n" +
		"* There is evidence of inverse growth between these two cell lines");
synapseClient.createWikiPage(myProject.getId(), ObjectType.ENTITY, page);
	{%endhighlight %}
	{% endtab %}


    {% tab Python %}
	{% highlight bash %}
projWiki = Wiki(title='Data Summary', owner = myProject )
markdown = '''* Cell growth look normally distributed
* There is evidence of inverse growth between these two cell lines '''
projWiki['markdown'] = markdown
projWiki = syn.store(projWiki)
	{% endhighlight %}
	{% endtab %}

    {% tab R %}
	{% highlight r %}
placeholderText <- "* Cell growth look normally distributed\n* There is evidence of inverse growth between these two cell lines."
wiki <- Wiki(owner=myProject, title="Analysis summary", markdown=placeholderText)
wiki <- synStore(wiki)
	{%endhighlight %}
	{% endtab %}

    {% tab Web %}
Go to project page and click the **Tool button** and chose **Edit Project Wiki**.
    {% endtab %}
{% endtabs %}

# Organizing Data: creating Files and Folders

The `Files` tab houses a remote file system that you can utilize to share your project's data, code, results, and any other information pertinent to your research. Unlike the file system on your local computer, Synapse `Files` and `Folders` are identified by a unique identifier, are versioned, and can be linked to one another using the Synapse `Provenance` services. These `Files` and `Folders`, like all Synapse content, can be accessed either through the web or through one of our analytical clients using their unique Synapse ID.

Synapse `Folders` are used just as folders are on a local file system -- to organize and segment content. `Folders` can also contain (or be *parents* of) any number of other `Folders` and `Files`.

To add a `Folder`:

{% tabs %}

	{% tab Command %}
	{% highlight bash %}
synapse create Folder name="results" parentId=syn123  #where syn123 is replaced by the project Id
	{% endhighlight %}
	{% endtab %}

    {% tab Java %}
	{% highlight java %}
Folder resultsFolder = new Folder();
resultsFolder.setParentId(myProject.getId());
resultsFolder.setName("results");
resultsFolder = synapseClient.createEntity(resultsFolder);
	{%endhighlight %}
	{% endtab %}

    {% tab Python %}
	{% highlight python %}
results_folder = Folder(name='results', parent=myProject)
results_folder = syn.store(results_folder)

	{% endhighlight %}
	{% endtab %}

    {% tab R %}
	{% highlight r %}
resultsFolder <- Folder(name = "results", parentId = myProject$properties$id)
resultsFolder <- synStore(resultsFolder)
	{%endhighlight %}
	{% endtab %}

    {% tab Web %}
click the **Add Folder** link under the Tools Menu on the Files tab.
<br>
    {% endtab %}
{% endtabs %}
<br>

Synapse `Files` are also much like files on a local file system -- except they are web-accessible to anyone who has access, can be richly annotated (and queried on), can be embedded as links or images within a Synapse `Wiki`, and can be associated with a [DOI](https://en.wikipedia.org/wiki/Digital_object_identifier){:target="_blank"}. `Files` carry the Conditions for Use of the `Folder` they are placed into in addition to additional specific Conditions for Use they have on their own.

Let's upload a local file `data/cell_lines_raw_data.csv` into this newly created `Folder`. To follow along you can pick any file you have and replace the name with your chosen file. We will also attach some annotations to this file describing the content of the file. In the example, we will associate the key `foo` with the value `bar` along with two numerical annotations.

{% tabs %}

	{% tab Command %}
	{% highlight bash %}

synapse add data/cell_lines_raw_data.csv --parentId=syn123  --annotations '{"foo": "bar", "number1":42, "number2": 3.14}'

	{% endhighlight %}
	{% endtab %}

    {% tab Java %}
	{% highlight java %}
import org.sagebionetworks.repo.model.file.S3FileHandle;

Long storageLocationId = null;
Boolean generatePreview = true;
Boolean forceRestart = null;
S3FileHandle result = synapseClient.multipartUpload(file, storageLocationId, generatePreview, forceRestart);

FileEntity fileEntity = new FileEntity();
fileEntity.setDataFileHandleId(result.getId());
fileEntity = synapseClient.createEntity(fileEntity);
	{% endhighlight %}
	{% endtab %}

    {% tab Python %}
	{% highlight python %}


raw_data_file = File(path="data/cell_lines_raw_data.csv", parent=results_folder)
raw_data_file.foo = 'bar'
raw_data_file.number1 = 3.14159
raw_data_file.number2 = 42
raw_data_file = syn.store(raw_data_file)

	{% endhighlight %}
	{% endtab %}

    {% tab R %}
	{% highlight r %}

rawDataFile <- File("data/cell_lines_raw_data.csv", parent=resultsFolder$properties$id, annotations=list(foo="bar", number1="42", number2="3.1415"))
rawDataFile <- synStore(rawDataFile)

{% endhighlight %}
{% endtab %}

{% tab Web %}

click the **Upload or Link to File** link in the Tools Menu on the Files tab. Go through the dialogs.  Then click the **Annotations** button on the top right of the screen to add annotations.

    {% endtab %}
{% endtabs %}

<br>

## Local Folder and File Sharing Settings

Access to `Files`, `Tables`, and `Folders` is controlled by the **Sharing setting** that you select for your project. You may also set individual Sharing settings for specific `Files`, `Tables`, or `Folders` within a `Project`.

# Provenance and Tracking Content

<img style="float:right" src="/assets/images/example_provenance.png">

Synapse provides advanced capabilities for formally tracking the relationship between digital assets (e.g. data, code, analytical results) through the Synapse provenance system. This aides in disseminating data and results in ways that other users can reproduce and reuse. Using provenance allows you to track your analysis history and communicate and share a sequence of processing steps. Provenance relationships can, for example, be specified between raw data, analysis code and results that occur in a complex processing pipeline, regardless of where it is run. The Synapse web services for managing provenance expose a general data model based on the [W3C Prov spec](http://www.w3.org/2011/prov/wiki/Main_Page){:target="_blank"}. Central to the design, users are not required to use a particular execution environment or workflow tool. Instead, provenance can be specified by inserting calls to the Synapse web service layer into their normal workflows to record activity; pipelines may be created through simple scripting or by using workflow execution engines. The provenance system allows users to branch off workflows at any point in prior analyses, while maintaining detailed records of data, code, and environment versions needed to reproduce the work.

Provenance is most easily specified when you are uploading or editing a file in Synapse. To specify the provenance you specify the files used as input and any files that were executed to generate the `File`. Both used and executed can be either an arbitrary URL such as a reference to a code commit on Github, a file stored on an FTP site or references to items in Synapse. Here we demonstrate how to add a figure to Synapse and indicate that code from https://github.com/Sage-Bionetworks/synapseTutorials was used to generate the figure from the data in the file `data/cell_lines_raw_data.csv` that we uploaded previously:

{% tabs %}

{% tab Command %}
{% highlight bash %}
synapse add images/plot_2.png --parentId=syn123  \
--used raw_data_file  \
--executed https://github.com/Sage-Bionetworks/synapseTutorials
{% endhighlight %}
{% endtab %}

	 {% tab Java %}
	{% highlight java %}
import org.sagebionetworks.repo.model.provenance.*;

Activity newActivity = new Activity();
newActivity.setName("plot distributions");
newActivity.setDescription("Generate histograms for demo");
Set<Used> used = new HashSet<Used>();
UsedEntity usedEntity = new UsedEntity();
usedEntity.setWasExecuted(false);
Reference fileEntityReference = new Reference();
fileEntityReference.setTargetId(rawDataFileEntity.getId());
usedEntity.setReference(fileEntityReference);
UsedURL usedURL = new UsedURL();
usedURL.setWasExecuted(true);
usedURL.setUrl("https://github.com/Sage-Bionetworks/synapseTutorials");
used.add(usedEntity);
used.add(usedURL);
newActivity.setUsed(used);
newActivity = synapseClient.createActivity(newActivity);
    {%endhighlight %}
	{% endtab %}

    {% tab Python %}
	{% highlight python %}
plot2 = File("images/plot_2.png", parent=results_folder)
plot2 = syn.store(plot2, used=raw_data_file,
	executed='https://github.com/Sage-Bionetworks/synapseTutorials')

	{% endhighlight %}
	{% endtab %}

    {% tab R %}
	{% highlight r %}
plot2 <- File(path="/images/plot2.png", parentId=resultsFolder$properties$id)
plot2 <- synStore(plotFileEntity, used=rawDataFile,
    executed='https://github.com/Sage-Bionetworks/synapseTutorials',
    activityName="plot distributions",
    activityDescription="Generate histograms for demo")
    {%endhighlight %}
	{% endtab %}

    {% tab Web %}
click the **Upload or Link to File** button on the Files tab to upload image/plot_2.png.  After uploading click the **Tools** button and chose **Edit Provenance**.
    {% endtab %}
{% endtabs %}

## More Guides

Find additional information and tutorials through our <a href="/articles/">User Guide</a>.
