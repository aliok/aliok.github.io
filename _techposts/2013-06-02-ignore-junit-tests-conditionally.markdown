---
layout: post
title:  "Ignore Junit tests conditionally"
date:   2013-06-02 01:07:43
banner: "/post-assets/layout/ignore-junit-tests-conditionally.jpg"
alternate_banner: True
banner_attribution: "Picture by Popular Science Monthly"
banner_source: "http://commons.wikimedia.org/wiki/File:PSM_V45_D249_Milk_testing_laboratory_wisconsin_dairy_school.jpg"

---

In TRNLTK project I had several data files which I received with an academic license. Thus I cannot publish them to Github. In that case, some of the tests in the CI server, drone.io, fails because those files cannot be found. 
First I thought of adding `@Ignore` to the tests which use those files, but in my local environment I should better run those tests.
<!--more-->

So, I thought of a better solution:

* Create a system property to be set by Maven, based on the Maven profile in use
* Check that property at the tests and if not set, skip the test

By default,  the property will be set to false:

{% highlight xml %}
<properties>
  <hasBigParseSets>false</hasBigParseSets>
</properties>
{% endhighlight %}


It will be set, when the Maven profile *bigParseSets* is activated:

{% highlight xml %}
<profiles>
   <profile>
      <id>bigParseSets</id>
      <properties>
         <hasBigParseSets>true</hasBigParseSets>
      </properties>
   </profile>
</profiles>
{% endhighlight %}

Important thing here is, to use JUnit’s `Assume` functionality. With that, if an assumption is not correct, then the test will be reported as "skipped / ignored". 
If you use `if(..) return;` then your test will be shown as successful.

{% highlight java %}
@Test
public void shouldTryEveryPossiblePhoneticAttributeSet() throws IOException {
    Assume.assumeTrue(TestEnvironment.hasBigParseSets());
    ...
}
{% endhighlight %}

Last piece:

{% highlight java %}
public class TestEnvironment {
   private static final String HAS_BIG_PARSESETS = "hasBigParseSets";
   public static boolean hasBigParseSets(){
      return "true".equalsIgnoreCase(System.getProperty(HAS_BIG_PARSESETS));
   }
}
{% endhighlight %}

If you put your assumption in the setup method (method annotated with `@Before`), then the whole test class will be ignored in case of failed assumption.

Here is how to activate the profile:

{% highlight bash %}
mvn test -P bigParseSets
...
Tests run: 4, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.494 sec
{% endhighlight %}

Without the profile:

{% highlight bash %}
mvn test
...
Tests run: 4, Failures: 0, Errors: 0, Skipped: 2, Time elapsed: 0.227 sec
{% endhighlight %}

When you execute the tests outside Maven, e.g. within your IDE’s JUnit runner, then you need to pass the system property as `-DhasBigParseSets=true`.

Intellij IDEA is really smart at this. If you activate the profile, then the system profile is automatically passed to Intellij’s JUnit runner:
![Screenshot](/post-assets/ignore-junit-tests-conditionally-01.png)

![Screenshot](/post-assets/ignore-junit-tests-conditionally-02.png)


Just don’t forget to press popping “Import” link when you switch profiles:
![Screenshot](/post-assets/ignore-junit-tests-conditionally-03.png)

(Or enable auto import to not do this every time)

I checked the alternatives a little bit. There is a good library named [junit-ext](https://code.google.com/p/junit-ext/) which allows you to do the same thing in a better way 
with `@RunIf(Condition.class)` annotations, but it seems dead.

Other alternatives include:

* using a JUnit `@Category` : Cannot specify something like : "Run all tests except this category". Even if you can, cannot integrate it well with Maven (+SureFire)
* using JUnit `@Rule`s : Need to implement your own annotation and extend standard JUnit runner, resolver, etc