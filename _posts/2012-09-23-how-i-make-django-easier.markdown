Everyone who has worked with Django for any reasonable period of time comes to the realization that starting up a new project can be a pain in the butt if you don’t build some level of automation into the process.  After a few times copy and pasting into your <pre>settings.py</pre> most developers decide they can’t handle it any more and write some scripts to take care of the setup process for them.

I thought I would share my setup script, recently modified, and hopefully it will be helpful to those who are still in copy and past land.

First, my basic setup for most django projects I start is as follows:

1)   create a new virtual environment with [virtualenv](https://pypi.python.org/pypi/virtualenv)

2)   install django within the environment and start a new project

3)   remove database setup, static media setup and debug settings from the auto generated settings.py file.

4)   Add debug\_toolbar settings, a dynamic media path, dynamic template\_dir path,  django\_evolution or south and an import of a settings\_local.py file to the settings.py file.

5)   Create a settings\_local.py file that defines local database connection, static media path and debug settings.

6)   Modify the urls.py file to handle your static and media directories correctly.

7)   Create a new sublime text project containing the new django project.

8)   Initialize the django project as a git repo  and create a .gitignore file.

9)   Create a deploy script to ssh into production and git pull from github.

10)  Get started actually building my app

Do this once or twice and you start to understand why many prefer customization over configuration.  But if you’re a Django lover like myself you find a way around it.

So I built this quick and dirty script to do all of this for me:

{% highlight python linenose=table %}
  import subprocess, os, sys, re
  from random import choice
  from git import *
  from django_create import *

  def main():
      #variable declarations
      starting_path = os.getcwd()
      templates_path = os.path.abspath(os.path.dirname(__file__)) + '/templates'
      project_name = sys.argv[1]

      create_virtualenv(project_name)
      create_django_project(project_name, starting_path)
      create_settings_file(project_name, starting_path, templates_path)
      create_secret_key()
      create_local_settings_file(project_name, templates_path)
      create_other_files(project_name, starting_path)
      create_git_repo(project_name, starting_path, templates_path)
      create_deploy_script(project_name, starting_path, templates_path)

  def create_virtualenv(project_name):
      subprocess.call(['virtualenv', project_name])

  def create_django_project(project_name, starting_path):
      os.chdir('/'.join([starting_path, project_name]))
      subprocess.call(['source', 'bin/activate'])
      subprocess.call(['bin/pip', 'install', 'django'])
      subprocess.call(['django-admin.py', 'startproject', project_name])

  def create_settings_file(project_name, starting_path, templates_path):
      print 'Creating custom settings.py file'
      os.chdir(starting_path + '/' + project_name + '/' + project_name)
      settings_file = open('settings.py', 'w')
      settings_template = open(templates_path + '/settings.py')
      settings_file.write(settings_template.read().replace('{{ project_name }}', project_name))
      settings_file.close()

  def create_secret_key():
      print'Creating secret key'
      settings_contents = open('settings.py', 'r').read()
      fp = open('settings.py', 'w')
      secret_key = ''.join([choice('abcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*(-_=+)') for i in range(50)])
      settings_contents = re.sub(r"(?<=SECRET_KEY = ')'", secret_key + "'", settings_contents)
      fp.write(settings_contents)
      fp.close()

  def create_local_settings_file(project_name, templates_path):
      print 'Creating custom settings_local.py file'
      settings_local_file = open('settings_local.py', 'w')
      settings_local_template = open(templates_path + '/settings_local.py')
      settings_local_file.write(settings_local_template.read().replace('{{ project_name }}', project_name))
      settings_local_file.close()

  def create_other_files(project_name, starting_path):
      print 'Creating Sublime Text project files'
      os.chdir(starting_path + '/' + project_name)
      sublimetext_project = open(project_name + '.sublime-project', 'w').write('{"folders": [{"path": "' + project_name + '"}]}')

  def create_git_repo(project_name, starting_path, templates_path):
      repo = Repo.init(''.join([starting_path, '/', project_name, '/', project_name]))
      git_ignore = open(starting_path + '/' + project_name + '/' + project_name + '/.gitignore', 'w')
      git_ignore_contents = open(templates_path + '/.gitignore').read()
      git_ignore.write(git_ignore_contents)
      git_ignore.close()

  def create_deploy_script(project_name, starting_path, templates_path):
      print 'Creating remote deploy script'
      os.chdir(starting_path + '/' + project_name + '/' + project_name)
      deploy_script = open('deploy.sh', 'w')
      deploy_contents = open(templates_path + '/deploy.sh', 'r').read().replace('{{ project_name }}', project_name)
      deploy_script.write(deploy_contents)
      deploy_script.close()
      subprocess.call('chmod 700 deploy.sh')

  if __name__ == '__main__':
      main()
{% endhighlight %}

This calls to a templates directory that has a settings.py settings_local.py and deploy.sh templates.

A few other things to note: most actions are called as subprocesses directly to the command line rather than using wrapper libraries primarily out of laziness.  When replacing the settings file it is important to remember to regenerate the secrete_key.  The deploy script is not an integration script.  For most of my personal projects I run tests locally and push to github for integration.

If you want to use this script or something similar simply clone [https://github.com/DanielNill/django_create](https://github.com/DanielNill/django_create) and modify as you like.
