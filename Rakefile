public_dir = "public"
source_dir = "source"
deploy_dir = "_deploy"
posts_dir = "blog"
projects_dir = "projects"
bios_dir = "teams"
stash_dir = "_stash"

git_url = "git@github.com:thinkdeciduous/engblog.git"
git_branch = "gh-pages"
git_master = "master"
git_user = "thinkdeciduous"

dirs = [posts_dir, projects_dir, bios_dir]

desc "Install Jekyll and all it's dependencies."
task :install do
	# check to see if dependencies are available, if not then download them and create the public folder
	begin
		require "jekyll"
		require "redcarpet"
		require "launchy"
		abort("Already installed all dependencies! Run \"rake deploy\" if you have not already to initialize the authoring environment")
	rescue LoadError
		puts "Installing Ruby dependencies..."
		system "gem install jekyll"
		system "gem install redcarpet"
		system "gem install launchy"
		system "mkdir public"
	end
end

desc "Setup the git-pages deployment area and initialize the repo for blog commits"
task :deploy do
	abort("Blog environment has already been deployed!") if File.directory?(deploy_dir)
	puts "Initializing authoring environment..."	
	system "mkdir #{deploy_dir}"
	cd "#{deploy_dir}" do
		# this all needs to be changed once I get the LinkedIn repo setup
		system "git init"
		system "git config user.name #{git_user}"
		system "git remote add origin #{git_url}"
		system "git pull origin"
		system "git checkout gh-pages"
	end
	#create the stash directory where files will be tracked for changes, used later to optimize jekyll build times
	FileUtils.mkdir("#{source_dir}/#{stash_dir}") unless File.exist?("#{source_dir}/#{stash_dir}")
	dirs.each do|dir|
		FileUtils.mkdir("#{source_dir}/#{stash_dir}/#{dir}") unless File.exist?("#{source_dir}/#{stash_dir}/#{dir}")
		FileUtils.cp Dir.glob("#{source_dir}/_posts/#{dir}/*"), "#{source_dir}/#{stash_dir}/#{dir}/"
	end
	puts "Building site (this could take a few minutes)..."
	system "jekyll build"
end

desc "Generate all static content with Jekyll and commit blog changes to github"
task :push do 
	#make sure the master branch is up to date
	system "git pull origin master"
	#check all files in the stash to see which files are new or have been modified, all unmodified files are removed so that only new or modified files are generated in a jekyll build
	dirs.each do|dir|
		Dir.glob("#{source_dir}/_posts/#{dir}/*") do |file|
			if(File.file?("#{source_dir}/#{stash_dir}/#{dir}/#{File.basename(file)}") && FileUtils.compare_file(file, "#{source_dir}/#{stash_dir}/#{dir}/#{File.basename(file)}"))
					File.delete(file)
			else
					FileUtils.cp file, "#{source_dir}/#{stash_dir}/#{dir}/#{File.basename(file)}"
			end
		end
	end
	system "jekyll build"
	#pull back in all unmodified files to keep everything synced up
	dirs.each do|dir|
		FileUtils.cp Dir.glob("#{source_dir}/#{stash_dir}/#{dir}/*"), "#{source_dir}/_posts/#{dir}/"
	end
	#make sure the static content is up to date
	cd "#{deploy_dir}" do
		system "git pull origin gh-pages"
	end
	#pull in all new content
	cp_r "#{public_dir}/.", deploy_dir
	cd "#{deploy_dir}" do
		system "git add -A"
		message = "Site updated at #{Time.now.utc}"
		system "git commit -m \"#{message}\""
		system "git push origin #{git_branch}"
	end
	#push all raw post markdown files into the master repo
	cd "#{source_dir}/#{posts_dir}" do
		system "git add -A"
		message = "Source upodated at #{Time.now.utc}"
		system "git commit -m \"#{message}\""
		system "git push origin #{git_master}"
	end
end

desc "Generate a temporary site and launch the Jekyll server"
task :preview do 
	require "launchy"
	out = ""
	go = false
	puts "Generating temporary site, and launching preview server..."
	jThread = Thread.new do
		until go do
			sleep 1
		end
		puts "Opening preview site in browser..."
		Launchy.open("http://localhost:4000")
	end
	IO.popen("jekyll serve") { |pipe| 
		until out.include? "Generating... done." do
		 	out = pipe.gets
		end 
		go = true
	}
	while jThread.alive? do
		sleep 1
	end
	system "rm -rf public/_site"
end