Class-based and Generic Views
 
Estimated time needed: 30 minutes
In this lab, you will create your class-based and generic views.
Learning Objectives
•	Understand class-based and generic views
•	Create class-based views to handle HTTP requests and send HTTP responses
•	Working with files in Cloud IDE
•	If you are new to Cloud IDE, this section will show you how to create and edit files, which are part of your project, in Cloud IDE.
•	To view your files and directories inside Cloud IDE, click on this files icon to reveal it.
•	 
•	Click on New, and then New Terminal.
•	 
•	This will open a new terminal where you can run your commands.
•	 
Concepts covered in the lab
1.	Class-based views (CBVs): In Django, class-based views refer to a method of defining views using classes instead of functions. CBVs provide a more structured and reusable approach to handling HTTP requests and generating responses.
2.	Generic views: Generic views in Django are a type of class-based view that offers pre-defined, reusable views for common use cases. They aim to reduce repetitive code and simplify common web development patterns.
3.	Function-based views (FBVs): In Django, function-based views are a way of defining views using regular Python functions. They are the simplest and most basic approach for handling HTTP requests and generating responses in Django.
Import an onlinecourse App Template and Database
1.	If the terminal was not open, go to Terminal > New Terminal and make sure your current Theia directory is /home/project.
•	Run the following command-lines to download a code template for this lab
1.	1
2.	2
3.	3
1.	wget "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-CD0251EN-SkillsNetwork/labs/m5_django_advanced/lab1_template.zip"
2.	unzip lab1_template.zip
3.	rm lab1_template.zip
Copied!Executed!
Your Django project should look like following:
 
2.	First, we need to install the necessary Python packages.
•	Change to the project folder:
1.	1
1.	cd lab1_template
Copied!Executed!
•	Install these must-have packages and setup the environment.
1.	1
2.	2
3.	3
4.	4
5.	5
1.	pip install --upgrade distro-info
2.	pip3 install --upgrade pip==23.2.1
3.	pip install virtualenv
4.	virtualenv djangoenv
5.	source djangoenv/bin/activate
Copied!Executed!
1.	1
1.	pip install -r requirements.txt
Copied!Executed!
Open myproject/settings.py and find DATABASES section and you can see that we use SQLite database in this lab, which is a file-based embedding database with some course data pre-loaded.
3.	Next activate the models for the onlinecourse app.
•	Perform migrations to create necessary tables:
1.	1
1.	python3 manage.py makemigrations
Copied!Executed!
•	and run migration to activate models for onlinecourse app.
1.	1
1.	python3 manage.py migrate
Copied!Executed!
In our previous labs, we only created function-based views, i.e., each view is a function to receive a HTTP request and return a HTTP response. In this lab, we will be focusing on creating class-based views.
Create Class-based Views
Open onlinecourse/views.py, you should note that the previous function-based course list, course enrollment, and course details views have been commented out.
In this lab, we will create class-based views to return the same HTTP response for those commented out function-based views. You could compare the difference between a function-based or a class-based view.
1.	First, let's create a class-based course list view
•	Open onlinecourse/views.py, add a CourseListView class with a get() method to handle HTTP GET request.
1.	1
2.	2
3.	3
4.	4
5.	5
6.	6
7.	7
8.	8
9.	9
1.	# Note that we are subclassing CourseListView from base View class
2.	class CourseListView(View):
3.	
4.	    # Handles get request
5.	    def get(self, request):
6.	        context = {}
7.	        course_list = Course.objects.order_by('-total_enrollment')[:10]
8.	        context['course_list'] = course_list
9.	        return render(request, 'onlinecourse/course_list.html', context)
Copied!
In the get() method, the top-10 popular courses were queried based on the field total_enrollment. The course list is appended to context and render an HTML page using onlinecoures/course_list.html template.
2.	Next, we need to configure the route for the CourseListView
•	Open onlinecourse/urls.py, add the following path entry to urlpatterns list:
1.	1
1.	path(route='', view=views.CourseListView.as_view(), name='popular_course_list'),
Copied!
Note that for the view argument, we actually added the as_view() method for CourseListView class. For function-based view, we use the view function name
directly in view argument.
3.	Next, we can try to create an enrollment class view to handle course enrollment.
•	Open onlinecourse/views.py, add a EnrollView class with a post method to handle HTTP POST request
1.	1
2.	2
3.	3
4.	4
5.	5
6.	6
7.	7
8.	8
9.	9
10.	10
1.	class EnrollView(View):
2.	
3.	    # Handles post request
4.	    def post(self, request, *args, **kwargs):
5.	        course_id = kwargs.get('pk')
6.	        course = get_object_or_404(Course, pk=course_id)
7.	        # Increase total enrollment by 1
8.	        course.total_enrollment += 1
9.	        course.save()
10.	        return HttpResponseRedirect(reverse(viewname='onlinecourse:course_details', args=(course.id,)))
Copied!
•	Open onlinecourse/urls.py, add the following path entry to urlpatterns list:
1.	1
1.	path(route='course/<int:pk>/enroll/', view=views.EnrollView.as_view(), name='enroll'),
Copied!
Same as the CourseListView, we added the as_view() method for the view
argument.
4.	Now we have created class-based view for returning a course list, let's start the development server to test it.
1.	1
1.	python3 manage.py runserver
Copied!Executed!
•	Click on the Skills Network button on the left, it will open the “Skills Network Toolbox”. Then click the Other then Launch Application. From there you should be able to enter the port 8000 and launch.
 
When the browser tab opens, add the /onlinecourse path and your full URL should look like the following
https://userid-8000.theiadocker-1.proxy.cognitiveclass.ai/onlinecourse
You should see a course list generated by the class-based CourseListView

