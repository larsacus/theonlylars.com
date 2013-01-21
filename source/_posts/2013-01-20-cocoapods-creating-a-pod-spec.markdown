---
layout: post
title: "Cocoapods: Creating a Pod Spec"
date: 2013-01-20 14:58
comments: true
published: true
categories: ruby iOS development open-source cocoapods
description: "Creating cocoapods specs can be difficult if you are new to cocoapods or haven't created a spec before. Here is a collection of documentation links, steps and tips for successfully creating a cocoapods pod spec file for your open source component."
---

_Note: Cocoapods is all very new and in rapid development (currently v0.16.1 as of this writing), if you are in the know and any of the below is wrong or incorrect, please let me know and I'll change it._

{% img left /images/posts/cocoapods/cocoapods_logo.png 200 200 %}If you're reading this there is a good chance you have at least heard of [Cocoapods](http://cocoapods.org). For the uninitiated, Cocoapods is a dependency manager for Mac and iOS projects built on Ruby and based on the Ruby community's "[rubygem bundler](http://gembundler.com)". Each "pod" has a "spec" that is utilized in order to know how to integrate a given component with your project and resolve any dependencies your project may have with other libraries. I'll be referring to these from here on out as either a "podspec" or more simply - a "spec".

Since I've had some trouble easily writing my podspecs in the past (and have run into poorly-tested specs in the specs repository), I have put together a collection of knowledge on the things I have learned while building and testing my podspecs.

I'm assuming that you already have cocoapods installed, and that you are looking to build and test a new `.podspec` file for a component.

##What is a Spec?
The basis behind how cocoapods is able to function is on a project's "podspec". Podspecs are created by maintainers of a project (or sometimes just other developers who want to use a component as a pod) and submitted to a public repository of specs in an organized git repository on github.

Specs identify everything about a library or component that needs to be performed before you can properly use it in your project. This includes everything from supported platform, ARC-readiness and required frameworks to other C flags that might need to be switched on.

##Basic Requirements
At a minimum, your spec needs to have the following attributes defined in order to be valid. If you think about it, if any one of these were missing, the spec itself wouldn't really make sense:

- Component name
- Semantic version number
- Platform
- Summary
- Author info dictionary
- Component homepage
- License
- Source location
- Source files list
- Any component dependencies*
- Required apple frameworks*

###Semantic Versioning
Cocoapods [_highly_ suggests](https://github.com/CocoaPods/CocoaPods/wiki/Contributing-to-the-master-repo) using (as in - don't _not_ do it) [semantic versioning](http://semver.org) to version your cocoapods. Without semantic versioning, it becomes much more difficult if not impossible to resolve some cross-dependencies between similar pod dependencies in your project, if any exist. All that was very complicated to say - use semantic versioning (v1.1.0). Tag your code in your repository with a tag corresponding to the version number of your component (for v3.0.0 of your component, tag your code `3.0.0`).

###Dependencies
The dependency line is technically optional if your component does not rely on any external pod.

###Frameworks
The frameworks line is also technically optional if your pod only uses the standard apple frameworks.

##Construction
A Podspec is [constructed by building a new `Spec` object](https://github.com/CocoaPods/CocoaPods/wiki/A-pod-specification) using a ruby "block". My ruby-fu is still pretty green, so I won't try and explain the ruby-ness of a podspec. To create a podspec using a block, we're going to declare the following in a new file named `<component_name>.podspec`. Please don't literally put the angle brackets in your file name:

{% codeblock lang:ruby AwesomeComponent.podspec %}
Pod::Spec.new do |s|
  # pod customization goes in here
end
{% endcodeblock %}

This creates a new `Spec` object that is included in the `Pod` library and inserts it into the local variable `s` for us to customize before it is returned. I've gone ahead and filled in some basic information for a spec I wrote for LARSAdController to demonstrate:

{% codeblock lang:ruby LARSAdController.podspec %}
Pod::Spec.new do |s|
  s.name         = 'LARSAdController'
  s.version      = '3.0.0'
  s.summary      = 'Lightweight ad mediation for iOS'
  s.author = {
    'Lars Anderson' => 'youremail@here.com'
  }
  s.source = {
    :git => 'https://github.com/larsacus/LARSAdController.git',
    :tag => '3.0.0'
  }
  s.source_files = 'Source/*.{h,m}'
  s.dependency     'AdMob'
end
{% endcodeblock %}

This will include all source files with file extensions `.h` & `.m` in the `Source/` folder in the github repository `https://github.com/larsacus/LARSAdController.git` in the source tag `3.0.0` while also including the pod for the dependency of `AdMob`.

###Hashes
If you're unfamiliar with ruby, you'll notice some interesting `{}` structures being assigned to variables like `source` and `author`. These structures are called "hashes" and they are exactly like your standard dictionary in objective-c:

`key => value`

For the `source` hash, the colon (`:`) before the key name is important. This [defines things like :git and :tag as ruby "symbols"](http://rubylearning.com/satishtalim/ruby_hashes.html) that are defined within cocoapods.

###Tags and Unversioned Repositories
There are other methods of specifying the location of source code instead of tags, but you should really be using tags to mark your code commit for your spec. If you are not the owner of the repository you are creating a spec for, [file an issue with the repository](https://github.com/CocoaPods/CocoaPods/wiki/Contributing-to-the-master-repo) to request that a tag be made. If this doesn't work, then you should specify the version of your podspec to begin at `0.0.1` to indicate that this pod's spec is unmanaged. Should the owner finally get a tag, then you can update the podspec with the correct version with little fear of conflicting with your previously unmanaged podspec's version.

###Version Simplification
Since this is just a ruby file, you can use all of the ruby tricks on any string in this file. To simplify our podspec for revving our version later, you can tell our `source` hash to use the podspec's version as the repository's tag to use using the `to_s` helper:

{% codeblock lang:ruby %}
Pod::Spec.new do |s|
  ..
  s.source = {
    :git => 'https://github.com/larsacus/LARSAdController.git',
    :tag => s.version.to_s
  }
  ...
end
{% endcodeblock %}

##Completing Your Spec
This is where writing a podspec gets a little complicated. The general example specs from the wiki are just fine, but when you start customizing your spec to the specific needs of your component or library, you can easily find yourself running to google with few available resources.

###Help
For the majority of the issues you will run into when creating your podspec, the [cocoapods wiki](https://github.com/CocoaPods/CocoaPods/wiki/) will have a page that will answer your question. Almost every issue I was having, I eventually found the answer to in the wiki. It could be a little better organized, because I really can't complain too much since I haven't submitted a pull request to fix it myself.

If you simply cannot find an answer anywhere, submit an issue on the [Cocoapods/Specs](https://github.com/CocoaPods/Specs/issues) repository (not the Cocoapods/Cocoapods repo) and someone is usually knowledgable enough to figure out what you're doing wrong or help you file a bug to fix the issue.

###Learn By Example
If you have a component or library that is built a specific way and you're having an issue, try and find a similar component that is built like yours and try that Pod's spec configuration. For instance, if your component is a static library, find a spec (like [Testflight](https://github.com/CocoaPods/Specs/blob/master/TestFlightSDK/1.2/TestFlightSDK.podspec)) that is built utilizing a static library.

##Testing
I found that this step was by far the most difficult to perform. I was not about to simply deploy a podspec that I had not tested, so this left me not even wanting to build a podspec for my component. I'm going to spare you the workflow iterations I tried before arriving at what I believe to be the "correct" way to test your podspec.

_I also understand that I am fully able to submit pull requests to modify the cocoapods wiki to make it more clear. This is something that I simply have not done, yet. This is way easier for now._

##Spec Lint
The first step to testing your spec is to use cocoapods' built-in "lint" tool to test for syntax compliance and minimum requirements. In your terminal prompt, run the lint tool on your podspec using:

{% codeblock lang:bash %}
pod spec lint "<spec location>/SpecName.podspec"
{% endcodeblock%}

If anything is syntactically wrong with your spec, it will show up here after running lint. Refer to the [cocoapods wiki](https://github.com/CocoaPods/CocoaPods/wiki) if any errors or warnings come up.

##In-Project (Functional) Testing
It's _very_ important to actually test your pod in a _fresh_ project without any other configuration. Don't be the guy that creates a spec, submits it, then you get a bunch of complaints because your spec doesn't actually install properly when it's installed. It's also annoying to find a spec you can use, put it as a dependency on your project, only to find out it's not configured properly. It looks bad on the cocoapods community when this happens.

Please functionally test your specs before submitting them:

1. Create a new empty xcode project
2. Create your `Podfile`
3. Add your pod as a project dependency
4. Specify local file path for new podspec

This is where I had a huge issue testing my podspec. The trick I learned is to [specify a local file path](https://github.com/CocoaPods/CocoaPods/wiki/Dependency-declaration-options) for your Podfile to look for your new podspec:
  
{% codeblock lang:ruby %}
pod 'LARSAdController', '3.0', :local => '~/Specs/LARSAdController'
{% endcodeblock%}

Now you can install and test your pod:

4. Install pod
  - `pod install` (initial install)
  - `pod update` (subsequent installs)
5. Test to ensure your component works
6. Rinse, repeat until component installs correctly

##Subspecs
A cool feature of cocoapods is it's ability for you to specify sub-components of your component to include. This will enable other developers to be able to a-la-carte choose which sub-components of your library to include in their project. This reduces the bloat, especially when only some parts of your project have dependencies on other large components.

Subspecs are totally optional in your podspec.

###Defining a Subspec
To define a [subspec](https://github.com/CocoaPods/CocoaPods/wiki/The-podspec-format) in your spec file, define a `subspec` variable (?) on your spec variable - much like we did in creating the spec itself:

{% codeblock lang:ruby %}
Pod::Spec.new do |s|
  ..
  s.subspec 'iAds' do |a|
    a.source_files = 'Source/iAds/*.{h,m}'
    a.dependency 'LARSAdController/Core'
    a.frameworks = 'QuartzCore'
    a.weak_frameworks = 'AdSupport'
  end
  ...
end
{% endcodeblock%}

Give your subspec a descriptive name, specify the source files to use, etc. In the above example, I have named this subspec 'iAds' since this will be including iAd functionality in with my pod. You'll continue to customize the subspec in the same way that you customized your parent spec object. Note that the subspec will inherit all of the base properties of the parent spec, so there is no need to repeat them in your subspec.

There are some properties that are not allowed to be defined on a subspec and can only be defined on the parent spec. These "Top level attributes" are outlined on the [podspec page](https://github.com/CocoaPods/CocoaPods/wiki/The-podspec-format) on the Cocoapods wiki.

Continue to create additional subspecs for however many sub-specifications that you need to create using the template:

{% codeblock lang:ruby %}
s.subspec 'SubspecName' do |a|
  # Customize
end
{% endcodeblock%}

###Final Product
In the end, you should have a podspec that looks a lot like mine does [for LARSAdController](https://github.com/CocoaPods/Specs/blob/master/LARSAdController/3.0.0/LARSAdController.podspec).

###Depending on a Subspec
To use the subspec in a Podfile, define the dependency like normal, but suffix it with a `/` and the subspec name:

{% codeblock lang:ruby %}
pod 'LARSAdController/iAds', '3.0'
{% endcodeblock%}

##Releasing
Congratulations! You've now completed the hardest parts of constructing your podspec. This next part is easy as long as your podspec lints correctly and installs - which you've already tested, right? In order to contribute to the [Cocoapods/Specs](https://github.com/CocoaPods/Specs) repository, you will need to clone the repo, add your spec and submit a pull request to have your spec added.

###Forking/Cloning
To add your spec to the list of podspecs, you will need to fork the [Cocoapods/Specs](https://github.com/CocoaPods/Specs) repo using your own github account. If you have cloned a fork of the repository and haven't committed to it even in the last 12 hours, you will want to update your repository by doing a `git pull` on your repo specifying the original remote location to pull the latest podspecs from. This repository gets updated quite frequently, so make sure you are up to date.

You will then need to clone the repository on your local machine and add your spec using the established file structure:

`ProjectName/Version/ProjectName.podspec`

So for the last release of LARSAdController, I put my latest podspec in `LARSAdController/3.0.0/LARSAdController.podspec` - one podspec per version folder.

Once this is added, you'll want to add the files to git, commit, and push them back up to your own fork of the Specs repo.

###Pull Request
Once the podspec is committed and pushed to your fork on github, you can submit a new pull request to merge the changes you made in your fork to the master repository that cocoapods is pulling their spec library from.

The friendly, hard-working folks who actively work to get your components merged in will then make sure that everything is linted and working correctly (syntactically, but not functionally) before allowing your changes to be merged into the master specs branch.

If this succeeds, then your podspec will now be available for everyone to use in their own projects! Pat yourself on the back - it wasn't all that bad, was it?
