---
layout: blog
title: Blog
---

  <div class="section-title">
    <h2><span> Blog </span></h2>
  </div>
  <div class="row listrecent">
    {% for post in site.posts %} 
      {% if post.tag == "travel" %}
        <div class="col-lg-4 col-md-6 col-sm-12 mb-4 card-group">
          <div class="card h-100">

            <div class="max-thumb"> </div>

            <div class="card-body">
              <h2 class="card-title">
                <a class="text-dark" href="{{ post.url }}">{{ post.title }}</a>
              </h2>
              <h4 class="card-text">
                {{ post.excerpt }}
              </h4>
            </div>

            <div class="card-footer bg-white">
              <div class="wrapfooter">

              <span class="author-meta">
                 <span class="post-date"> {{ post.date  | date_to_string }}</span>
              </span>
              </div>
            </div>
          </div>
        </div>
   {% endif %}
  {% endfor %}




</div>