---
date: 2020-07-24
title: Customising API Visibility
linktitle: Customising API Visibility
menu:
  main:
    parent: "Customise"
---

By default, any user who accesses your developer Portal will be able to view all of the published API's in the catalog. This behavior may not be desired and you may want to have more control of what API's developers see in the catalog when access the portal. A common use case for this is if you have internal API's that you want to publish but only want your internal developers to be able to see these in the catalog.

We'll walk through how you can use custom Page Templates to control the visibility of an internal API so it can only be seen by your internal developers.

## Prerequisites
1. You have An API created in your Dashboard. See [Create an API](/docs/try-out-tyk/tutorials/create-api/) for more details.
2. You have a Policy created in your Dashboard that has access rights to this API
3. You have a Portal Catalog entry for this API with the name of "Internal API"
4. You have a developer account that can access your Developer Portal.


## Add a custom field to the developer profile

For this example, we'll add a custom field to the developer profile called "internal". This flag when set to 1 indicates the developer is an internal developer, when set to 0 indicates the developer is an external developer.
Go to the Portal Management > Developers screen

![dev_profile_custom_field](/docs/img/dashboard/portal-management/dev_profile_custom_field.jpg)


This flag can also be [set programatically](https://tyk.io/docs/tyk-developer-portal/customise/custom-developer-portal/#updating-a-developer-example-adding-custom-fields).


## Modify the Portal Catalogue Template to add Show/Hide Logic

The developer portal is fully customizable via templates. We'll add custom logic to the portal catalogue template (catalogue.html) to show/hide the "Internal API" catalogue based on the value of the "internal" flag for the developer.  

Please see the customized catalogue template ​​here​: 

<details>
<summary>Click to expand template</summary>

```text
{{ define "cataloguePage" }} {{ $org_id := .OrgId}} 
{{ template "header" .}}
{{ $page := .}}
<body>

	{{ template "navigation" . }}

	<div>

		<!-- Main content here -->

		<div class="container" style="margin-top:80px;">
		
		<div class="row">
		
			<h1>API Catalogue</h1>
		</div>
			
			<div class="row">

			{{ if .Data.APIS }}
				{{if .UserData.Fields}}
					{{$internal := index .UserData.Fields "internal"}}
					{{ range $index, $apiDetail := .Data.APIS}}
						{{ if $apiDetail.Show }}
							{{if (and (eq $apiDetail.Name "Internal API") (eq $internal "0") )}}
									<p>Internal Catalogue cannot be shown to external developer. {{ printf "(catalogue name: %#v)" $apiDetail.Name }} </p>

							{{else}}
								<div class="col-md-4">
					<h2>{{$apiDetail.Name}}</h2>
					<p>{{$apiDetail.LongDescription | markDown}}</p>

					{{ if $apiDetail.Documentation }}


					<a href="{{ $page.PortalRoot }}apis/{{$apiDetail.Documentation}}/documentation/" class="btn btn-info catalogue">

				
	    				<span class="glyphicon glyphicon-book" aria-hidden="true"></span>&nbsp; View documentation 	
	    			</a>
					<br/>

					{{ end }}

					{{if eq $apiDetail.Version "" }}
					{{if eq $apiDetail.IsKeyless false}}

					<a href="{{ $page.PortalRoot }}member/apis/{{$apiDetail.APIID}}/request" class="btn btn-success catalogue">

						<span class="glyphicon glyphicon-ok-sign" aria-hidden="true"></span>&nbsp; Request an API key
					</a>
					{{ end }}
					{{ else }}
					{{if eq $apiDetail.IsKeyless false}}
    				<a href="{{ $page.PortalRoot }}member/policies/{{$apiDetail.PolicyID}}/request" class="btn btn-success catalogue">
    					<span class="glyphicon glyphicon-ok-sign" aria-hidden="true"></span>&nbsp; Request an API key
    				</a>
					{{ end }}
					{{ end }}
				</div>
							{{ end }}
						{{ end }}
					{{ end }}
				{{ else }}
					{{ range $index, $apiDetail := .Data.APIS}}
						{{ if $apiDetail.Show }}
							{{if (ne $apiDetail.Name "Internal API") }}
								<div class="col-md-4">
									<h2>{{$apiDetail.Name}}</h2>
									<p>{{$apiDetail.LongDescription | markDown}}</p>

									{{ if $apiDetail.Documentation }}


									<a href="{{ $page.PortalRoot }}apis/{{$apiDetail.Documentation}}/documentation/" class="btn btn-info catalogue">


										<span class="glyphicon glyphicon-book" aria-hidden="true"></span>&nbsp; View documentation
									</a>
									<br/>

									{{ end }}

									{{if eq $apiDetail.Version "" }}
									{{if eq $apiDetail.IsKeyless false}}

									<a href="{{ $page.PortalRoot }}member/apis/{{$apiDetail.APIID}}/request" class="btn btn-success catalogue">

										<span class="glyphicon glyphicon-ok-sign" aria-hidden="true"></span>&nbsp; Request an API key
									</a>
									{{ end }}
									{{ else }}
									{{if eq $apiDetail.IsKeyless false}}
									<a href="{{ $page.PortalRoot }}member/policies/{{$apiDetail.PolicyID}}/request" class="btn btn-success catalogue">
										<span class="glyphicon glyphicon-ok-sign" aria-hidden="true"></span>&nbsp; Request an API key
									</a>
									{{ end }}
									{{ end }}
								</div>
							{{end}}
						{{end}}
					{{end}}
				{{ end }}
			{{ else }}
				<div class="row">
				<p>
					<em>It looks like there are no APIs in the Catalogue.</em>
				</p>
				</div>
			{{ end }}
		</div>
	</div>
	{{ template "footer" .}}
	</div>
	<!-- /container -->
	{{ template "scripts" .}}
</body>
</html>
{{ end }}
```
</details>

We're now going to overwrite the default catalogue.html template in the 'portal/templates' directory on the Tyk Dashboard instance with the custom one above.

**NOTE**: After replacing or updating a template, the Dashboard must be restarted to apply the changes.

Now the visibility of the "Internal API" is driven by the internal flag on the developer profile.


#### Developer Logged In, internal flag set to 1 (Internal API is visible)
![dev_logged_in_internal](/docs/img/dashboard/portal-management/dev_logged_in_internal.jpg)

#### Developer Logged In, internal flag set to 0 (Internal API not visible)
![dev_logged_in_external](/docs/img/dashboard/portal-management/dev_logged_in_external.jpg)

#### No User Logged In (Internal API not visible)
![no_user_logged_in](/docs/img/dashboard/portal-management/no_user_logged_in.jpg)
